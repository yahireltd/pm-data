---
id: T-0333
title: Route the remaining refund-creating Stripe paths through the gateway (deposit refunds, manual refund form, year-end, legacy endpoint)
type: chore
state: triaged
created: 2026-06-09T22:34:27Z
updated: 2026-06-10T15:23:34Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: Claude (T-0331 build)
  channel: claude-code
assignee: null
acceptance_criteria:
  - Every refund-creating path (deposit refund service x2, manual refund form, year-end handler, refunds controller if it moves money) goes through the gateway factory
  - "A decision is recorded for the deprecated old refund endpoint: deleted, or converted and guarded"
  - On a sandbox environment, exercising each converted flow produces dry-run log rows and zero live Stripe activity
  - A final grep inventory confirming no direct Stripe client construction remains outside the factory is attached to this ticket
out_of_scope:
  - Payment-taking flows (checkout/payment links) — they create charges, not refunds; assess separately
  - Read-only Stripe usages (charge/payout retrieval) — convert opportunistically but they cannot move money
code_anchors:
  - path: common/services/DepositRefundService.php
    lines: 189,316
  - path: common/models/ManualRefundForm.php
    lines: "98"
  - path: common/services/YearEndRefundHandler.php
    lines: "134"
  - path: backend/controllers/RefundsController.php
    lines: "130"
  - path: backend/controllers/ContractsController.php
    symbol: actionProcessContractRefundsOld
    lines: 1323-1790
  - path: common/services/StripeGatewayFactory.php
    symbol: create
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - refunds
  - payments
  - testing
  - incident-c090586
attention: null
version: 3
---

## Problem

T-0331 makes the main payment-refund flow physically unable to send real Stripe refunds from the test environment. But an inventory taken during that build found five other code paths that still construct the Stripe client directly and can move real money — so the test box (nightly copy of live, possibly carrying the live key) remains live-armed through:

1. The deposit refund service (the Process Deposit Refunds button) — creates refunds in two places.
2. The manual refund form (the Stripe option on the manual refund screen) — creates refunds.
3. The year-end refund handler — creates refunds.
4. The refunds controller — check what it does and whether it moves money.
5. The DEPRECATED old process-refunds endpoint — superseded but still routable; it can create refunds with no guard at all. Strongly consider deleting it outright instead of converting it.

(Two further constructions in the payments controller are read-only — charge/payout retrieval — lower priority but convert for consistency.)

## What to do

Replace each direct construction with the gateway factory from T-0331, using the same two operations (retrieve charge / create refund) — mostly mechanical one-line swaps plus adapting the call sites. Decide deletion vs conversion for the deprecated endpoint and record it.

## Testing plan

Per converted path, on the test box (sandbox): exercise the flow end-to-end (deposit refund on a contract with a held deposit; manual refund form with the Stripe method; year-end path if reachable), verify the flow completes, rows are written as normal, the dry-run log captures the would-be request, and the LIVE Stripe dashboard shows zero activity. Re-run the grep inventory at the end: zero direct StripeClient constructions outside the factory (excluding read-only/commented ones, which are listed in the ticket on completion).

## Conversation

**2026-06-10 14:27 claude-code:** **Refund-creating bypass paths converted (2026-06-10, on branch refund-hardening-t0331-stripe-dryrun, uncommitted pending Austin's review):**

- `DepositRefundService` — both sites (processNew post-transaction loop, processLegacy) now go through `StripeGatewayFactory::create()` with contract context; the unused `Stripe\StripeClient` import removed.
- `ManualRefundForm::saveRefund` — converted, import added.
- `ContractsController::actionProcessContractRefundsOld` — both sites converted. **Decision recorded: converted, not deleted.** Rationale: zero in-repo references exist (it is dead-but-routable), but this branch must carry no live behaviour change (T-0332 rollout rule: T-0331 deploys first, alone). Recommend deleting the action in a later cleanup once live access logs confirm nothing external posts to it.
- `RefundsController::actionUpdateStripeRefunds` — left on the raw client (retrieve-only, cannot move money) but hardened: it now skips synthetic `dryrun_*` refund ids, so a `stripeDryRun` rehearsal on live can no longer crash the balance-transaction backfill loop.
- Legacy `require '../../vendor/autoload.php'` lines dropped from converted blocks (composer autoload is loaded by index.php; verified these paths are web-only).

**Post-conversion grep inventory** (acceptance criterion): `refunds->create` now exists in exactly one live code path — `LiveStripeGateway::createRefund` — plus three commented-out blocks (XeroOld/XeroNew/YaContracts). Remaining direct client constructions are all read-only: RefundsController:~130 (refunds->retrieve), PaymentsController:232/243 (charge/payout retrieve), SiteController (checkout, payment-taking — out of scope per ticket).

**Bonus hardening from adversarial review (2 reviewers, 0 blockers):** `DryRunStripeGateway::createRefund` now computes the locally-known refundable balance and flags requests that exceed it (`stripe_dryrun_requests.wouldExceedRefundable` = 1, plus a Yii warning) — without blocking. The legacy paths never call retrieveCharge, so dry-run would otherwise report "succeeded" on exactly the over-refund requests live Stripe rejects — the C090586 failure mode. Migration amended in place (it has not been applied anywhere yet). Three new unit tests cover flag = 0 / 1 / NULL-unknown.

**Known limitations recorded:** (1) dry-run synthesises payment_intent from local stripe_payments — a nightly-copy row with NULL paymentIntentID will fail StripeRefunds validation loudly (HTTP 500) where live might succeed; (2) pre-existing `$contract->contractID` vs `$contract->id` inconsistency at the old endpoint's split-payment site (ContractsController ~line 1711) left untouched — live behaviour unchanged, but on the test box stripe_dryrun_requests.contractID and StripeRefunds.contractID can disagree at that one site.

**2026-06-10 15:23 claude-code:** Committed as `59b861fe` on branch refund-hardening-t0331-stripe-dryrun (local only, not pushed). The branch is now three commits ahead of master and self-contained: gateway harness (a9615bdd), review fixes (84444136), bypass closures + dry-run hardening + the C090586 forensic query pack (59b861fe). Test-server walkthrough lives on T-0331 (comment of 2026-06-10 14:27).
