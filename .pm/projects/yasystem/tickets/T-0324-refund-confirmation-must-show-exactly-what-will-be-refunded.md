---
id: T-0324
title: Refund confirmation must show exactly what will be refunded
type: bug
state: triaged
created: 2026-06-09T19:24:53Z
updated: 2026-06-09T19:54:20Z
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
assignee: null
acceptance_criteria:
  - "The refund modal lists each individual refund that will be executed: source payment reference, method (Stripe/BACS), and amount, plus the grand total"
  - The preview figures are derived from the same planning logic the executor runs, not a separate calculation that can drift
  - Generating the preview performs zero database writes and zero Stripe refund creations (read-only Stripe lookups are acceptable)
  - For a fixture reproducing C090586's pre-refund state, the modal shows the full £1,399.85 plan (or, once the T-0320 guard is enforced, the blocked state) — never a different number to what would execute
  - For a normal contract with a simple credit-note refund the preview matches the executed refund to the penny
out_of_scope:
  - Changing what gets refunded (guard and calculation fixes are separate tickets)
  - Redesigning the modal beyond the figures shown
code_anchors:
  - path: backend/views/contracts/manual-refund.php
    symbol: refundModal totals
    lines: 255-330
  - path: backend/views/contracts/manual-refund.php
    symbol: "#refundModal markup"
    lines: 580-689
  - path: common/services/RefundPlanner.php
    symbol: plan
  - path: common/services/PaymentAllocator.php
    symbol: allocate
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - refunds
  - payments
  - incident-c090586
attention: null
version: 3
backlog_status: confirmed_for_release
estimated_effort: M
source: discovered
---

## Problem

In the C090586 incident the refund modal told the user "Refund £138" but clicking the button refunded £1,399.85. The number on screen and the action behind the button are calculated by two different pieces of code that disagree: the on-screen total nets positive and negative "unused" amounts against each other, while the execution path silently drops the negatives and refunds every positive in full. The user approved a number that had nothing to do with what happened.

## Build order

Depends on **T-0331 (dry-run)** for testing and ideally lands after **T-0320 (guard)** so the preview can also show the blocked state. Third or fourth in the sprint.

## Context

The fix is to make the modal display the actual refund plan — the same plan the executor will run: one line per refund showing source payment, method (Stripe or BACS), and amount, plus the total.

CRITICAL TRAP for whoever builds this: the allocation step of the refund pipeline writes to the database — you cannot "dry-run" the existing pipeline as-is to build the preview. The preview must be computed side-effect-free. The planner itself is safe (its Stripe calls are read-only), but anything that produces the planner's inputs must be a calculation, not the mutating allocator. Suggested shape: extract a pure "what would allocation leave over" calculation that both the page and (eventually) the allocator share, then feed the planner.

## Testing plan

**All on the test box (sandbox + dry-run):**

1. **No-side-effect proof:** enable the DB query log, open the refund modal on a contract with unallocated payments and unused credits. Expected: zero INSERT/UPDATE statements from building the preview. This is the test for the trap above — make it an automated test if feasible, not just a one-off check.
2. **The incident fixture:** on the reconstructed C090586 pre-refund state (same recipe as T-0320), the modal must list the full plan — £1,255.85 Stripe from the card payment, £6 and £138 BACS — totalling £1,399.85 (or, with the T-0320 guard enforced, show the blocked state with both figures). It must never show a number different from what would execute.
3. **Preview-vs-execution diff (the core regression):** for each of the 5–10 reconstructed real refunds from T-0320's replay set: capture the preview lines, execute in dry-run, then diff preview vs the dry-run request log + written refund rows. Every line must match on source payment, method, and amount, to the penny. Automate this comparison if at all possible — it is the durable regression test for this whole area.
4. **Stripe-balance edge:** a contract whose card charge has already been partially refunded (planner caps to the remaining refundable balance) — preview must reflect the capped amount, not the naive balance.
5. **Allocate-then-refund path:** a customer with a second unpaid contract — preview must show what will be allocated where AND what will then be refunded, matching execution.
6. **Empty state:** a contract with nothing refundable — the button should not present a misleading non-zero figure (today's screen only shows the button when the running balance is negative; keep that behaviour consistent with the new preview).
7. **Cross-impact:** the figures on the statement table itself (running balance column) are produced by separate display code — confirm the page still renders identically elsewhere, and note any remaining display/calculation mismatches here rather than silently widening scope.