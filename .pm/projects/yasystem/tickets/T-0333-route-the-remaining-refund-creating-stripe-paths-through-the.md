---
id: T-0333
title: Route the remaining refund-creating Stripe paths through the gateway (deposit refunds, manual refund form, year-end, legacy endpoint)
type: chore
state: triaged
created: 2026-06-09T22:34:27Z
updated: 2026-06-09T22:34:27Z
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
version: 1
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