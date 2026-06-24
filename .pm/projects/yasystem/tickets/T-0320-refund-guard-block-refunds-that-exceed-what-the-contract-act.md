---
id: T-0320
title: "Refund guard: block refunds that exceed what the contract actually holds (shadow mode first)"
type: feature
state: done
created: 2026-06-09T19:14:46Z
updated: 2026-06-24T20:08:30Z
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
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260610-1813
    model: claude-fable-5
    started: 2026-06-10T18:13:52Z
    status: in_progress
    policy_ack:
      branch: master
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-06-10T18:13:52Z
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - refunds
  - payments
  - incident-c090586
attention: null
version: 9
backlog_status: confirmed_for_release
estimated_effort: M
source: discovered
branch: refund-hardening-t0331-stripe-dryrun
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

## Conversation

**2026-06-10 18:14 claude-code:** **Guard built, adversarially reviewed, and integration-tested on the test box (2026-06-10, commit f79fc027 on refund-hardening-t0331-stripe-dryrun, pushed with Austin's authorisation).**

**Implementation:** `common/services/RefundGuard.php` — cap = cash_in − refunds_out − max(owed, 0). cash_in attributes payments by their FIRST allocation row by id (deliberately date-agnostic: immune to the T-0326 blind spot that caused the incident), net of cross-contract outflows (allocations and refunds recorded on sibling contracts). owed = type-0 invoices − type-1 credit value not consumed by other contracts − deposit charges, clamped at zero (credit-exceeds-payments cannot manufacture never-received cash; IFNULL on voided). Wired into `actionProcessContractRefunds` (types 1/2, after allocation, before the gateway/planner — an enforced block makes zero Stripe calls, even read-only) AND into `YearEndRefundHandler::refund` (review found it runs the identical pipeline). Modes off/shadow/enforce via `params['refundGuardMode']`; **absent param = shadow**, so the live deploy needs no config and cannot block anyone. Shadow swallows guard-internal failures (the guard must never break a refund); enforce fails closed. Every check logs to `runtime/logs/refund-guard.log` (file, survives the rollback); over-cap hits also log a customer-level cap to separate attribution noise from real danger. 10 unit tests (mode resolution, comparison tolerance, short-circuits).

**Adversarial review (2 lenses):** caught one real blocker pre-deploy — the original formula ignored cross-contract OUTflows, re-admitting the incident class for multi-contract customers (donor-side cap inflation, widened by the type-1 flow's own allocations). Fixed before commit. Warnings recorded: legitimate refunds sit exactly at cap (zero slack — shadow analysis must bucket near-cap pennies separately before promotion); receiving-side strictness will warn on cross-contract-settled contracts (expected, ticket-accepted); concurrent double-click window remains until T-0321's lock; the error message's "nothing recorded" was softened because generateInvoice runs pre-transaction.

**Integration results (test box, all on the reconstructed incident fixture):**
1. SHADOW + incident fixture: refund executed (shadow never blocks) and logged `REFUND-GUARD OVER-CAP planned £1,399.85, cap £138.00, margin −£1,261.85, customerCap £138` — the phantom named to the penny, with full per-item breakdown and user. This is the line live's daily shadow review watches for.
2. ENFORCE + incident fixture: blocked with the on-screen message naming both figures; verified ZERO writes (payment_refunds 0, stripe_refunds 0, cnas 0, dry-run rows unchanged) and zero Stripe calls. **The C090586 incident is now impossible to execute on this code path.**
3. ENFORCE + reconstructed real refund (C090897, £791.88, deposit-held — sits EXACTLY at cap, the strictest pass test): executed identically to the original live refund, guard logged `ok, margin 0`.
4. Cap probe over post-refund states: all settled contracts compute cap = 0.00 exactly (accounting identity holds on real data); post-incident C090586 computes cap = −£1,261.85 — the phantom — so repeat attempts are also blocked.

**Remaining for this ticket:** re-run the full 4-replay set under enforcement after tonight's restore re-arms the fixtures (1 of 5 shapes done under enforce); construct the credit-exceeds-payments and cross-contract edge fixtures; then the live shadow window (deploy → daily log review → promotion decision recorded here). Test box left in ENFORCE (`common/config/params-local.php`); live deploys with no param = shadow.

**2026-06-15 19:14 claude-code:** **Reconciliation 2026-06-15 (Austin confirmed the testing record = the 2026-06-10 session in chat; all of it passed as expected). Verified against the test box's refund-guard.log, which survives nightly restores:**

The log holds exactly 4 entries, all 2026-06-10 19:04–19:12, matching what was done:
- 19:04:38 SHADOW, incident fixture C090586 → logged OVER-CAP, planned £1,399.85 vs cap £138.00, margin −£1,261.85, customerCap £138 (executed, shadow never blocks).
- 19:07:48 + 19:11:41 ENFORCE, incident fixture → blocked; separately verified zero writes (payment_refunds 0, stripe_refunds 0, cnas 0, dry-run rows unchanged) and the on-screen 422 naming both figures.
- 19:12:18 ENFORCE, C090897 £791.88 (sits EXACTLY at cap, margin 0, deposit-held) → passed, executed identically to the original live refund.

Plus a cap probe over post-refund states: settled contracts compute cap 0.00 exactly; post-incident C090586 computes −£1,261.85 (repeat attempts also blocked).

**Acceptance-criteria status (accurate):**
- ✅ Pre-flight check after allocation, before any row/Stripe call
- ✅ Shadow logs contract/planned/cap/breakdown/user
- ✅ Enforce returns clear error, zero rows, zero Stripe (row-count + dry-run verified)
- ✅ Incident fixture blocked (enforce) and logged (shadow)
- ⏳ 5–10 real replays UNDER ENFORCE: only C090897 executed under enforce; the other 4 (C091790, C087727, C089519, incident) were matched to the penny in DRY-RUN under T-0331 *before the guard existed*, not re-run under enforcement. Outstanding.
- ⏳ Edge cases (credit-exceeds-payments, cross-contract, SC contract, repeat-attempt): not yet run as recorded tests
- ⏳ Live shadow window + promotion decision: not started (needs deploy)
- ⏳ Process Deposit Refunds unaffected: deposit flow is separate and ungated by T-0320, but a confirmation pass is still owed

**Operational note for whoever continues:** the test box is currently on branch `PickingSketchSalesDashFriday` and `RefundGuard.php` is NOT on that checkout — the box is not running the guard right now. To resume guard testing it must go back on `refund-hardening-t0331-stripe-dryrun` (or that branch merged forward). That branch has also since picked up two unrelated commits (a3608323 account segmentation PP-002, 489cf7c9 customer sales scores) that would ride along on any deploy — worth separating before the live shadow rollout.

---

**2026-06-24 20:08 — you**

Tested again extensively on 24th june confirmed 3000 refunds would be the same ( barring penny differences )
