---
id: T-0331
title: Stripe dry-run mode for the test environment (log would-be requests, never call Stripe)
type: feature
state: in_progress
created: 2026-06-09T19:49:27Z
updated: 2026-06-09T22:34:37Z
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
labels:
  - refunds
  - payments
  - testing
  - incident-c090586
attention: null
version: 7
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