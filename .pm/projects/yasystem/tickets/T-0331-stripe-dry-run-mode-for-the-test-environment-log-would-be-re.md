---
id: T-0331
title: Stripe dry-run mode for the test environment (log would-be requests, never call Stripe)
type: feature
state: in_progress
created: 2026-06-09T19:49:27Z
updated: 2026-06-10T16:49:05Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "With the sandbox flag on, no code path in the payment-refund flow can reach Stripe's refund-creation endpoint (verified by test: the live client is never constructed in sandbox mode)"
  - In dry-run mode, processing a refund writes one log row per would-be Stripe request capturing charge reference, amount in pence, idempotency key, contract, and user, and the on-screen flow completes as normal
  - In dry-run mode, charge balance lookups are answered from local data so refundable-balance calculations work on the nightly-copied database
  - Live mode behaviour is request-for-request identical to today — verified by unit tests against a stubbed client and by replaying 3–5 recent real refunds in dry-run and matching their logged requests to live's recorded Stripe refunds
  - The test server's currently-configured Stripe secret has been checked and the finding recorded on this ticket
  - A documented way exists to review the dry-run log (simple query or screen) so a human can verify what would have been requested
  - "Cross-impact: all other Stripe client usages (payments, deposits, payouts, webhooks) confirmed untouched"
out_of_scope:
  - Stripe payment-taking flows (payment links, deposits) — same risk, assess separately
  - The replay/diff harness itself (build once dry-run mode exists, if still wanted)
  - The deposit refund service
code_anchors:
  - path: common/services/StripeGateway.php
    symbol: StripeGateway interface
  - path: common/services/LiveStripeGateway.php
    symbol: LiveStripeGateway
  - path: common/services/DryRunStripeGateway.php
    symbol: DryRunStripeGateway
  - path: common/services/StripeGatewayFactory.php
    symbol: create / mustDryRun
  - path: common/models/StripeDryrunRequests.php
    symbol: StripeDryrunRequests
  - path: console/migrations/m260609_220000_create_stripe_dryrun_requests_table.php
  - path: common/services/RefundPlanner.php
    symbol: refundablePence — now takes the gateway
    lines: 25-50
  - path: common/services/RefundExecutor.php
    symbol: execute — createRefund via gateway
  - path: backend/controllers/ContractsController.php
    symbol: actionProcessContractRefunds — factory wiring
    lines: 1836-1852
  - path: common/tests/unit/services/StripeGatewayFactoryTest.php
  - path: common/tests/unit/services/DryRunStripeGatewayTest.php
  - path: backend/config/main.php
    symbol: sandbox flag
    lines: 11-21
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260609-2221
    model: claude-fable-5
    started: 2026-06-09T22:21:56Z
    status: in_progress
    policy_ack:
      branch: master
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-09T22:21:56Z
    progress:
      - at: 2026-06-09T22:34:05Z
        note: "Code written, NOT yet run anywhere (per Austin's instruction — code only for now). On branch refund-hardening-t0331-stripe-dryrun, uncommitted pending Austin's go-ahead (protected project). Built: the gateway interface, live passthrough, dry-run implementation (charge state synthesized from local stripe_payments + stripe_refunds rows, both confirmed to store consistent units; would-be refunds logged to a new stripe_dryrun_requests table via migration), the fail-safe factory (sandbox flag forces dry-run unconditionally; optional stripeDryRun param forces it elsewhere), rewired the planner/executor/controller to use it, and two test files: factory wiring tests (DB-free) and gateway behaviour tests (DB-dependent, marked to run on the test box only). All files pass PHP syntax lint. FINDING for follow-up: an inventory of remaining direct Stripe client constructions shows the deposit-refund service, the manual refund form, the year-end refund handler, the refunds controller, and the deprecated old refund endpoint still bypass the gateway — the test box stays live-armed through those paths until they are routed through the factory too."
      - at: 2026-06-09T22:59:17Z
        note: "Multi-agent adversarial review of the first commit confirmed two blockers, both now fixed, committed (84444136d on branch refund-hardening-t0331-stripe-dryrun) and pushed with Austin's authorisation. Blocker 1: the recorded Stripe payment amounts are stored in pence, not pounds — the dry-run charge synthesis would have reported every charge 100x too large; verified against the site checkout writer (it stores the exact value sent to Stripe) and fixed, with tests moved to pence fixtures. Blocker 2: the year-end refund handler still built the planner/executor with a raw Stripe client, which the new type would crash on — it now goes through the gateway factory, which as a bonus brings year-end refunds under the sandbox fail-safe (one item of T-0333 done early). Also hardened: failed/cancelled refund records no longer shrink the synthesized refundable balance, would-be requests are additionally written to the file log so audit evidence survives a transaction rollback, and the user lookup is console-safe. Remaining for this ticket: apply the migration on the test box, run both test files there, end-to-end dry-run refund check, and confirm which Stripe secret the test server loads."
labels:
  - refunds
  - payments
  - testing
  - incident-c090586
attention: null
version: 16
backlog_status: confirmed_for_release
estimated_effort: M
source: discovered
---

## Problem

The refund hardening sprint needs extensive testing, but the test system (nightly copy of the live database) cannot safely or even usefully talk to Stripe: the copied data contains live charge references, which a Stripe test key cannot see — and if the test server is configured with the live key, testing refunds there would send real money to real customers. There is currently nothing in the code that stops that.

## Build order

**Build this FIRST — every other sprint ticket's testing plan depends on it.** No dependencies of its own.

## Implementation outline

1. Define a small gateway interface covering the only two Stripe operations the refund flow uses: retrieve a charge, create a refund.
2. Live implementation: passthrough to the existing Stripe client — request-for-request identical to today.
3. Dry-run implementation:
   - Charge lookup answered from local data: amount from the recorded Stripe payment row, amount_refunded from the sum of recorded Stripe refund rows for that charge reference.
   - Refund creation writes one row to a new log table (suggested: stripe_dryrun_requests via the standard migration pattern) capturing charge reference, amount in pence, idempotency key, contract ID, user ID, timestamp — then returns a synthetic refund object (id like dryrun-<n>, status succeeded) so downstream record-writing proceeds normally.
4. Wiring: a single factory decides which implementation the refund controller gets. When the sandbox param is true the dry-run gateway is returned unconditionally — no configuration can override it.
5. The planner's static refundable-balance helper currently takes the Stripe client directly; route it through the gateway too.

## Testing plan

**Environment: local/test server (sandbox=true, nightly DB copy). No live deploy until all steps pass.**

1. **Unit — dry-run charge lookup:** for a payment row with a recorded Stripe payment of £100 and one recorded refund of £30, the gateway reports £70 refundable. Cover: no refunds yet, fully refunded, multiple partial refunds, charge reference missing.
2. **Unit — dry-run refund creation:** creating a £25 refund writes exactly one log row with amount 2500 pence and the right charge ref/idempotency key, and returns a synthetic object the executor accepts.
3. **Unit — fail-safe wiring:** with sandbox=true the factory returns the dry-run gateway even when a live-looking secret key is configured. This is the test that matters most.
4. **Integration on the test box:** pick a real contract from the nightly copy with a modest genuine credit due, click Process Payment Refunds end-to-end. Verify: (a) the flow completes without error, (b) refund/allocation rows are written as normal, (c) the dry-run log contains the expected request(s), (d) the LIVE Stripe dashboard shows zero refund activity for that charge during the test window.
5. **Live-mode regression (read path only):** in live mode, the charge-retrieve passthrough returns the same values the old direct call did — verify with a unit test against a stubbed client, NOT by running refunds on live.
6. **Replay sanity check:** take 3–5 refunds that really happened on live in the last month; reconstruct their pre-refund state on the test box (delete the refund rows in the test DB only); re-run them in dry-run; diff the logged would-be requests against the real stripe_refunds rows from live. Amount and charge ref must match exactly. Any divergence is a finding, not a rounding error.
7. **Cross-impact check:** grep for every other construction of the Stripe client (payment links, deposits, payouts, webhooks) and confirm none of them route through the changed code path. This ticket must change refund behaviour only.

## First step before any code

Check the test server's local params file to confirm which Stripe secret it currently loads — if it is the live key, flag immediately as an operational risk independent of this build.

## Conversation

**2026-06-10 13:55 claude-code:** **Test-box safety gap relevant to this ticket's remaining work (2026-06-10 branch audit):** even once this branch is deployed to the test server, the box stays **live-armed** through three refund-creating paths that bypass the gateway factory: `ContractsController::actionProcessContractRefundsOld` (direct `refunds->create` at lines 1587 and 1694 — endpoint still routable), `DepositRefundService` (lines 189 and 316 — the end-of-hire refund modal path via actionProcessRefund/actionProcessBatchRefund), and `ManualRefundForm` (line 98). `RefundsController:130` also constructs a raw client but is retrieve-only (no money movement). YearEndRefundHandler is already fixed (commit 84444136). Recommendation: before any sprint integration testing on the test box, either route these three through `StripeGatewayFactory` (the pattern is established; each is a small change) or disable the old endpoint — otherwise a mis-click during testing can still send real money exactly as the original risk statement warned.

Status reminder of this ticket's outstanding items: apply migration m260609_220000 on the test box, run the two test files there, one end-to-end dry-run refund, and confirm which Stripe secret the test server loads (record finding here).

**2026-06-10 14:27 claude-code:** **Test-box safety gap closed (2026-06-10):** the three refund-creating bypass paths flagged in the previous comment (DepositRefundService x2, ManualRefundForm, old refund endpoint x2) are now routed through the gateway factory on this branch — see T-0333 for the full conversion record. With these changes, on a sandbox box **no code path can create a Stripe refund**: `refunds->create` exists only inside `LiveStripeGateway`, which the factory never returns when `params['sandbox']` is set.

**Dry-run log review (acceptance criterion "documented way to review the log"):**
```sql
-- everything the flow would have asked Stripe to do, newest first
SELECT id, created, endpoint, chargeRef, amount AS pence, contractID, userID,
       wouldExceedRefundable, idempotencyKey
FROM stripe_dryrun_requests ORDER BY id DESC;
```
`wouldExceedRefundable = 1` marks requests live Stripe would reject as exceeding the charge's refundable balance (new column, added before the migration has been applied anywhere). The same requests are mirrored to the file log (category `stripe.dryrun`) so evidence survives a transaction rollback.

**Test-server walkthrough for Austin (in order):**
1. Confirm `params['sandbox']` is true in the test box's params-local AND record which Stripe secret it loads (acceptance criterion — flag immediately if it is the live key).
2. Copy the branch across; run `php yii migrate` (applies m260609_220000 including the new column).
3. Run the unit tests on the test box: `vendor/bin/codecept run unit services/StripeGatewayFactoryTest` (DB-free) and `vendor/bin/codecept run unit services/DryRunStripeGatewayTest` (DB-dependent, @group db).
4. End-to-end: pick a real contract from the nightly copy with a modest genuine credit due → Process Payment Refunds → flow completes, refund/allocation rows written, dry-run log rows present, LIVE Stripe dashboard shows zero refund activity in the window.
5. Also exercise the newly-converted paths once each: Process Deposit Refunds on a deposit-held contract, and a manual refund with the Stripe method — same assertions.
6. Replay 3–5 recent real refunds (reconstruct-replay-diff, technique A on T-0332) — logged requests must match live's stripe_refunds to the penny.

Caveat for step 4/5: if a contract's stripe_payments row has chargeRef but NULL paymentIntentID, dry-run fails loudly with 'Refund Didnt save to Stripe Refunds Table!' — that's the synthetic-payment_intent limitation, not a regression; pick another contract.

**2026-06-10 15:29 claude-code:** **Clarification for the "confirm which Stripe secret the test server loads" criterion (2026-06-10, from code):** the sandbox flag does NOT switch Stripe keys anywhere in the codebase. `backend/config/main.php:11-32` uses `sandbox` to select only the database host (restored nightly copy vs live RDS) and year-end product IDs; the Stripe secretKey/publicKey always come from AWS Secrets Manager (lines 44-59) via the secret name in `params['sn']` (gitignored params-local.php on each box). There is no active `secretKeyTest` mechanism — the only references are commented-out Xero code.

So the check is: compare `sn` in the TEST box's common/config/params-local.php and backend/config/params-local.php against the live box's value. Same secret name = the test box holds the LIVE Stripe key (the exact live-armed risk this ticket exists to neutralise — the gateway now makes it survivable, but the better end state is a separate Secrets Manager secret for the test box holding a Stripe TEST key, so even read-only paths like RefundsController/PaymentsController retrieves cannot touch the live account). Record the finding here either way.

**2026-06-10 15:42 claude-code:** **Acceptance criterion closed — test server Stripe secret checked and finding recorded (2026-06-10):** Austin confirmed the test server's params-local uses the **same `sn` as live/local**, i.e. the test box was loading the **live Stripe key** while pointing at the test database — the live-armed risk this ticket predicted, now confirmed real.

**Mitigation shipped** in commit `b37cab9e` (pushed to refund-hardening-t0331-stripe-dryrun): both config entry points (backend/config/main.php, console/config/main.php) now override the Stripe keys whenever `sandbox=true` — they take `stripeTestPublicKey`/`stripeTestSecretKey` from the same Secrets Manager secret, defaulting to an **empty string** if those fields don't exist. Effect: the moment the test box pulls this branch, no code path on it (refund-creating, read-only retrieves, payment-taking, console jobs) holds the live key; calls fail loudly with an auth error instead of reaching the live Stripe account. Live boxes are untouched (sandbox unset → override never runs).

**Remaining manual step for Austin (optional but recommended):** add two fields to the existing Secrets Manager secret — `stripeTestPublicKey` (pk_test_…) and `stripeTestSecretKey` (sk_test_…) from the Stripe dashboard's test-mode keys. Until then the test box runs key-less, which is fine for all T-0331 testing: the dry-run gateway answers the refund flow from local data and never calls Stripe.

**2026-06-10 16:03 claude-code:** **Correction to the previous comment — finding updated (2026-06-10):** Austin checked the actual secret contents: the Secrets Manager secret shared by local and the test server (austinaccess) stores a **Stripe TEST key**, not the live key. So the test box was never live-armed through the key — the pre-branch worst case there was noisy API errors (test key cannot see live charge refs), not real refunds. The live server presumably loads a different secret carrying the live key.

The sandbox key override is amended accordingly in `857a474e` (replacing the blanket swap in `b37cab9e`): on sandbox boxes it now cascades — explicit `stripeTestPublicKey`/`stripeTestSecretKey` fields if present in the secret → otherwise the loaded key is KEPT when it carries a test-mode prefix (`pk_test_`/`sk_test_`) → otherwise blanked. Net effect: the test box's current working test key keeps working with zero AWS changes, and the only configuration that gets disarmed is the genuinely dangerous one — a sandbox box somehow holding a live-prefixed key. Live boxes (sandbox unset/false) are untouched by the whole block.

Acceptance criterion status: test server secret checked — **test key confirmed** — and the config now enforces that invariant going forward rather than trusting it.

**2026-06-10 16:09 claude-code:** **Migration/nightly-cron note (2026-06-10):** this branch adds exactly ONE migration (m260609_220000_create_stripe_dryrun_requests_table — purely additive, one new table). The test server's nightly 06:00 `yii migrate --interactive=0` cron handles it automatically: each restore removes both the table and its migration-history row together, and the cron re-creates it every morning while the test box is checked out on this branch. Consequences: (1) dry-run evidence rows in stripe_dryrun_requests are wiped nightly — capture same-day or use the file log (`stripe.dryrun` category in runtime logs), which survives restores; (2) confirm the restore completes before 06:00, else the table is missing until the next cron (manual migrate fixes it); (3) **live deploy checklist item: run `yii migrate` once on the main server when this branch merges** — after that the restored copy carries the table and the cron's involvement ends. First-time setup today is manual: `/usr/bin/php8.3 /var/www/yasystem/yii migrate --interactive=0`, then verify with `SHOW COLUMNS FROM stripe_dryrun_requests LIKE 'wouldExceedRefundable'`.

**2026-06-10 16:44 claude-code:** **Integration testing milestone (2026-06-10, test box): the C090586 incident replayed in dry-run, matching live to the penny.**

Sequence: unit tests green (factory 4/4 incl. the sandbox-always-dry-run fail-safe; gateway 11/11 incl. the wouldExceedRefundable cases) → two clean end-to-end dry-run refunds (C091852 £8.38, C088514 £56.40 — every artefact matched prediction; file audit log verified working after the log-target fix in 3576b98b and the cookie-dump fix in 285a04c4) → **incident fixture created** (the 25-05 refund artefacts deleted from the test DB with a full snapshot; phantom allocation 48031, £0 row 48262 and both credit notes restored to their 14:37 pre-click state) → Austin re-performed Jahzel's exact click (Stripe option).

Replay result vs the real 25-05 refund: Stripe leg £1,255.85 / 125585 pence on ch_3TWFmuKYqp01XEip1FmKBptS (identical), BACS legs £6.00 + £138.00 anchored to payment 47215 (identical; BR references fresh-random as designed), overpayment link and credit-note allocations structurally identical, total £1,399.85 — and the screen showed "£138" before the click, reproducing the T-0324 display/execution mismatch on current code. wouldExceedRefundable=0, correctly: the request exactly equals the charge's refundable balance — the over-refund is at contract level, which only the T-0320 guard can catch. Zero Stripe API traffic (dry-run; Austin to spot-check the live dashboard for ch_3TeH66/ch_3SuEqn/ch_3TWFmu over today's window).

This satisfies the replay acceptance criterion's most important datapoint and establishes the canonical T-0320 fixture: guard-in-shadow must log planned £1,399.85 vs cap £138 on this exact state; guard-enforced must block it. The fixture is recreatable any day (nightly restore brings the artefacts back; the reset script recipe is recorded in this run).

Remaining for this ticket: 2–4 more replay datapoints from ordinary recent refunds, exercise the two converted flows (Process Deposit Refunds on a deposit-held contract; manual refund form with Stripe method), and the live-dashboard spot check.

**2026-06-10 16:49 claude-code:** **Live Stripe dashboard check: CLEAN (2026-06-10).** Austin confirmed zero refund activity on the live Stripe account for all three charges exercised today (ch_3TeH66…, ch_3SuEqn…, ch_3TWFmu… — the latter being the C090586 incident charge replayed at full £1,255.85). Combined with the dry-run log rows and file audit trail, the integration acceptance criteria are met: the flow completes normally, writes its records, logs exactly what it would have sent, and provably never reaches Stripe. Remaining before closing this ticket: 2–4 replay datapoints from ordinary recent refunds, and one exercise each of the two converted flows (Process Deposit Refunds; manual refund form, Stripe method).
