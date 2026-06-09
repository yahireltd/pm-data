---
id: T-0320
title: "Refund guard: block refunds that exceed what the contract actually holds (shadow mode first)"
type: feature
state: triaged
created: 2026-06-09T19:14:46Z
updated: 2026-06-09T19:53:15Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p0
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - A pre-flight check computes the contract's net refundable cash position (money received minus money genuinely owed, including credit notes, deposits excluded) after allocation but before any refund rows are written and before any Stripe API call
  - With the shadow-mode flag on, over-cap refunds are NOT blocked but a log records contract number, planned refund total, computed cap, per-item breakdown, and user
  - With enforcement on, over-cap refunds return a clear error stating the planned total and the cap, and no rows are written and no Stripe request is made (verified by row counts and the dry-run log)
  - The reconstructed C090586 pre-refund fixture is blocked by the guard in enforced mode and logged in shadow mode
  - 5–10 reconstructed recent real refunds replay through the enforced guard unblocked, with dry-run requests matching live's actual Stripe refunds to the penny
  - "Edge cases pass: cross-contract allocation before cap, credit note exceeding payments, repeat refund attempt, deposit-held contract, sales (SC) contract"
  - Shadow mode has run on live for an agreed clean window with all over-cap hits investigated before enforcement is switched on; the promotion decision is recorded on this ticket
  - Process Deposit Refunds verified unaffected
out_of_scope:
  - Changing getUnusedPayment() semantics (separate ticket)
  - The deposit refund flow (Process Deposit Refunds button) — separate code path
  - Restructuring the Stripe-inside-transaction design (separate backlog ticket)
code_anchors:
  - path: backend/controllers/ContractsController.php
    symbol: actionProcessContractRefunds
    lines: 1790-1850
  - path: common/services/PaymentAllocator.php
    symbol: allocate
  - path: common/services/RefundPlanner.php
    symbol: plan
  - path: common/services/RefundExecutor.php
    symbol: execute
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

On contract C090586 a member of the accounts team processed what the screen said was a £138 refund, but the system actually sent the customer £1,399.85 — including a full £1,255.85 Stripe refund they were not owed. The customer themselves flagged it. There is currently no final safety check: each "unused" payment and credit note is refunded independently, with nothing asking "does this contract actually hold this much of the customer's money overall?"

## Build order

Depends on **T-0331 (Stripe dry-run mode)** — all integration testing below runs in dry-run on the test box. Build second in the sprint.

## Context

The wrong refund was triggered by corrupted allocation data (see T-0323/T-0325/T-0326), but the missing safety net is what let real money leave. The refund pipeline (allocator → planner → executor) is used by exactly one endpoint, so this change is well contained.

Two existing design constraints the guard MUST respect:

1. Stripe calls happen inside the DB transaction — if anything throws after a Stripe refund succeeds, the DB rolls back but the money is gone with no record (full write-up in T-0328). The guard must therefore run as a pre-flight check BEFORE the first Stripe call and before any rows are written, never mid-loop.
2. Roll out in shadow mode first: a config flag runs the guard log-only so the cap formula is validated against real refund traffic before it blocks anyone.

## Cap formula (agreed starting point — refine during build)

Net refundable for the contract = cash received attributable to this contract (payments where this is the original contract) minus what is genuinely owed (invoiced totals less unused credit notes), with deposits excluded (own flow; the planner already reserves unused deposits on shared charges). Strictly per-contract: the allocate-to-other-contracts step runs BEFORE the cap is computed, so legitimate cross-contract allocation still works — the cap only governs what is then refunded.

## Implementation outline

1. Compute the cap inside the refund action after allocation, before the planner/executor run.
2. Compare against the plan total. Shadow flag (suggested: params['refundGuardShadow']) decides log-vs-block.
3. Log line (both modes): contract number, planned total, computed cap, per-item breakdown, user.
4. Enforced mode: throw a clear HttpException naming planned total and cap; transaction rolls back; nothing written, no Stripe call made.

## Testing plan

**Stage A — test box (sandbox + dry-run), before any live deploy:**

1. **The incident fixture:** recreate C090586's pre-refund state in the TEST database by deleting the 25-05 refund artefacts for that contract only (three payment_refunds rows, their credit-note allocations, the stripe_refunds row, the overpayment link row). Click Process Payment Refunds. Expected: shadow mode logs planned £1,399.85 vs cap £138 (over-cap flagged); enforced mode blocks with both figures in the message, writes nothing, dry-run log shows zero Stripe requests.
2. **Happy-path replay:** reconstruct 5–10 recent REAL refunds the same way (pick a spread: pure credit note, genuine overpayment, BACS-only, card+BACS mix, contract with deposit held, sales contract SC-prefix). Re-run each in dry-run with the guard enforced. Expected: none blocked; would-be Stripe requests match what live actually sent, to the penny.
3. **Edge cases to construct manually on the test box:** customer with a second unpaid contract (allocate-then-refund path — cap must apply to the post-allocation remainder); credit note larger than all payments; refund re-attempted after a partial refund already exists; contract where VAT was removed after invoicing; £0.00-allocation payments present.
4. **No-write verification:** with enforcement triggering, confirm via query log / row counts that payment_refunds, credit-note allocations, and the dry-run request log all gained zero rows.

**Stage B — live, shadow mode (1–2 weeks):**

5. Deploy with shadow ON, enforcement OFF. The accounts team works normally.
6. Daily: review the guard log. Every over-cap hit is investigated as either (a) a real would-be incident — evidence the guard works, or (b) a false positive — fix the formula and restart the clock.
7. Promote to enforced only after a clean window with zero unexplained over-cap hits, and record the promotion decision on this ticket.

**Cross-impact:** deposit refund flow (separate endpoint) untouched — verify Process Deposit Refunds still works on the test box after the change.