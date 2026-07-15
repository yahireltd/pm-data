---
id: T-0426
title: Bacs refund happened when stripe was selected
type: feature
state: triaged
priority: p2
created: 2026-06-18T15:44:12Z
updated: 2026-07-15T15:55:50Z
project: yasystem
section: null
parent: null
milestone: null
children: []
order: 36864
reporter: null
assignee: null
acceptance_criteria:
  - Look into the cause as to why this happened
  - propose the correct fix (do not touch the code until we have agreed on the solution)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
---

https\://system.yahire.com/contracts/manual-refund?quoteOrContractNumber=C090850 ben chose stripe and it refunded by BACS anyway

## Conversation

**2026-07-15 15:48 — Austin Pickering**

Ben just tired 2 more of these. He selected stripe for 2 different refunds. One went by bacs and one went by stripe. It seems if the first payment on a contrat is BACS it will refund by bacs regardless of if we pick stripe. (obviously if we do refund to stripe there needs to be money left to refund on it (and if the deposit was paid by stripe in the same transaction as the payment we must reserve the deposit portion on stripe to refund) I thought i had taken care of this already but seems to be an issue with it

**2026-07-15 15:55 claude-code:** **Root cause found (code + verified against C090850's data). No code touched, per the ticket.**

## What actually decides the refund method

The user's Stripe/BACS choice travels correctly from the modal to the server (`useBacs` → `RefundExecutor::execute($contract, $plan, $forceBacs)`). But the executor has **two different code paths with different behaviour**:

1. **Surplus-payment refunds** (`RefundExecutor` §1, lines ~71–150): one refund row per payment that holds unused balance, refunded **by that payment's own method**. `forceBacs=true` is honoured (forces BACS), but **`forceBacs=false` (Stripe selected) is silently ignored** — if the surplus sits on a BACS payment, a BACS refund row is written with no Stripe attempt and no warning.
2. **Credit-note refunds** (§2): correctly Stripe-first — chunks through Stripe charges with refundable balance (respecting the deposit reserve via `refundablePence`), and only the uncoverable remainder falls back to a BACS anchor. This is the behaviour Austin remembers building — it just never reached path 1.

`PaymentAllocator` is method-blind (no preference parameter), so **which payment ends up holding the refundable surplus is an accident of allocation history** — that's the real correlation, not strictly "first payment BACS": whichever payment holds the unused balance dictates the method in path 1.

## C090850 evidence
- Payment 47235 STRIPE £554.41 (05-May), payment 47544 BACS £554.41 (15-May) — a double payment.
- The Stripe payment was consumed by the invoice; the £554.41 surplus sat on the **BACS** payment.
- Ben picked Stripe → refund 3197 anchored to payment 47544, reference BR99f8f74, **no stripeRefundID** → BACS. Path 1, exactly as coded. (Note: first payment here was STRIPE — disproving the first-payment theory; the surplus location is what matters.)
- Ben's two cases on 15-Jul: the one that "worked" will have had its refundable value flow through path 2 or sit on a Stripe payment.

## Proposed fix (for agreement before any code)

**Option A — recommended: give path 1 the same Stripe-first logic as path 2.** When Stripe is selected, route the surplus through Stripe charges with refundable balance (cross-payment, exactly as the credit-note path already does in production), regardless of which payment holds the surplus; only what Stripe can't cover becomes a BACS row — and the response/UI **reports the split explicitly** ("£X refunded to card, £Y recorded as BACS — manual transfer needed") instead of silence. C090850 shows this is the desired outcome: the card had £554.41 refundable; Ben's choice should have refunded it. Implementation notes: extract path 2's chunking into a shared routine; PaymentRefunds rows anchor to the Stripe payment actually refunded; PaymentRefundOverpayments keeps linking the surplus-source payment for audit; deposit reserve already handled by `refundablePence`.

**Option B — conservative: never switch silently, never guess.** If Stripe is selected but the refundable surplus can't be covered by Stripe charges, abort with a clear message ("the refundable balance sits on a BACS payment — choose BACS or re-allocate"). Zero accounting novelty, but would have blocked C090850's legitimate card refund.

**Optional enhancement either way:** when type=1 (allocate-then-refund) with Stripe selected, teach `PaymentAllocator` to prefer consuming non-Stripe payments into invoices, so the surplus lands on the Stripe charge and the fallback rarely fires.

## Test plan once agreed (sandbox, T-0331 dry-run gateway — no real Stripe calls)
1. Reproduce C090850's shape (stripe + bacs double payment, surplus on the BACS row), pick Stripe → expect a dry-run Stripe refund request for the full amount, zero BACS rows.
2. Stripe charge only partially refundable (deposit reserve) → expect split + the explicit split report.
3. Pick BACS → all-BACS, unchanged.
4. RefundGuard limits still enforced on both paths.
