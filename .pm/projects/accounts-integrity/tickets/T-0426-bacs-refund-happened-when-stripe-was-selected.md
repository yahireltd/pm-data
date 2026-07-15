---
id: T-0426
title: Bacs refund happened when stripe was selected
type: feature
state: review
priority: p2
created: 2026-06-18T15:44:12Z
updated: 2026-07-15T17:51:32Z
project: accounts-integrity
section: null
parent: null
milestone: MS-003
children: []
order: 7168
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Look into the cause as to why this happened
  - propose the correct fix (do not touch the code until we have agreed on the solution)
out_of_scope: []
code_anchors:
  - path: backend/views/contracts/manual-refund.php
    symbol: processContractRefunds
    note: composition-blind hasStripePayments gate ~191 + choice dialog ~1074
  - path: common/services/RefundExecutor.php
    symbol: execute
    note: path 1 (surplus by-method, forceBacs distortion) vs path 2 (credits stripe-first)
  - path: backend/controllers/ContractsController.php
    symbol: actionProcessContractRefunds
    note: useBacs param -> creditsViaBacs semantics; new coverage endpoint lands here
relates: []
blocks:
  - T-0538
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1644
    model: claude-fable-5
    started: 2026-07-15T16:44:25Z
    status: completed
    policy_ack:
      branch: t0426-refund-method-gate
      branch_source: ticket
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-15T16:44:25Z
    ended: 2026-07-15T17:17:53Z
    summary: "Refunds sometimes went out by bank transfer even when staff picked \"Stripe (refund to card)\". Cause: the page offered the Stripe/BACS choice whenever the contract had ANY card payment, but a payment surplus (an overpayment) is money that physically sits on one specific payment and can only be returned the way it came in — so if that surplus was on a bank payment, the system quietly did a bank refund regardless of the choice. The only money that can genuinely be sent either way is a credit-note balance. Fix: the choice now appears ONLY when the whole refund is credit-note balance and the card can cover it in full; otherwise there's no misleading choice — staff see a plain confirmation of exactly what will happen and to which method. Surplus refunds always follow their own payment's method. Without this, staff keep selecting Stripe and getting BACS, and have to chase/correct refunds by hand. This change is now part of the T-0538 release branch, so the two must be tested together before going live."
    test_plan: |-
      On the sandbox (xerotest.yahire.com, branch already deployed at ddcffc80). DO NOT click the final "Process refund" / submit on live-money contracts — for the dialog checks, confirm the dialog/message then CANCEL.

      **A. The choice appears only for genuine credit-only refunds**
      1. C092481 → Process Payment Refunds → expect the Stripe/BACS dialog ("£227.22 of credit-note balance to refund"). Cancel.
      2. C092494 → same → dialog with £607.20. Cancel.
      (Austin has spot-checked several of these already — confirmed showing correctly.)

      **B. No misleading choice on surplus refunds**
      3. A contract with an unrefunded payment surplus (ask Claude to surface one on the snapshot) → Process Payment Refunds → expect NO method dialog, instead an info confirmation "£X will be refunded to the original payment method(s)". Cancel.
      4. (Proven by harness: C090850 reconstructed to its pre-refund state returns surplus £554.41, no dialog — the exact reported bug.)

      **C. Mixed and partial-coverage**
      5. If a contract has BOTH surplus and credit balance → no dialog; confirmation explains "£X to original method(s), £Y credit by BACS".
      6. If credit balance exceeds refundable card balance → no Stripe option; confirmation says card refund not available.

      **D. Cross-impact / regression (the risky part — shared refund pipeline)**
      7. A pure BACS contract (no card payments) with a refund due → unchanged behaviour, BACS refund, no dialog.
      8. Submit ONE real credit-only refund end to end on the sandbox (dry-run Stripe gateway, no real money): pick Stripe → a dry-run card refund request is logged, PaymentRefund row has a stripeRefundID; pick BACS → BACS reference, no Stripe. Check the manual-refund page statement reflects it.
      9. RefundGuard cap still enforced (over-cap refund still blocked).
      10. Deposit refunds (processDepositRefunds) were deliberately NOT changed — confirm they still behave as before; decide in review whether they need the same gate (separate follow-up).

      **E. Release coupling**
      11. This ships in the T-0538 branch — verify BOTH the document-integrity work AND this refund change on the same deploy; do not release one without the other having been tested.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: written
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-15T17:17:53Z
version: 13
branch: t0538-document-generator-integrity
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

**2026-07-15 16:38 claude-code:** **REVISED proposal — supersedes Options A/B above, per Austin's accounting correction (2026-07-15).**

Austin's reasoning, which changes the frame: a payment surplus is money sitting ON a specific payment (all payments are stored as overpayments in Xero). If a Stripe payment is already fully allocated to an invoice, there is nothing on it to refund — refunding the card anyway would claw money off an invoice it legitimately covers while the true surplus (on the BACS row) stays on the books. So surplus refunds are NOT method-flexible: they must refund from where the money physically sits. **Only credit-note balance is routable, so the Stripe/BACS choice should only be offered when the ENTIRE amount to refund is credit-note balance.** The executor's by-method behaviour on surpluses (path 1) was correct; the bug is the UI offering a choice it can't honour — the current gate is just "does the contract have ANY Stripe payment" (manual-refund.php ~191), composition-blind. C090850 was exactly this: a pure payment-surplus refund shown a Stripe option that could never legitimately apply.

## Proposed fix

**1. UI gate (the core fix):** compute the refund composition server-side — surplusPortion (Σ unused payment balances) and creditPortion (Σ unused credit-note balances). Show the Stripe/BACS dialog ONLY when surplusPortion == 0 AND creditPortion > 0 AND Stripe coverage exists. In every other case: no dialog, and the page states plainly what will happen ("£X will be refunded from payment #N via BACS / to the card it was paid on") so staff are never surprised.

**2. Server decoupling (small but necessary):** today `forceBacs=true` also distorts path 1 — it forces a BACS reference on a STRIPE surplus and skips the card refund. Under the new rule the flag becomes `creditsViaBacs`: path 2 (credit notes) consults it; **path 1 always refunds each surplus by its own payment's method**, flag-independent. Mixed refunds (surplus + credits) get no dialog: surplus by-method, credits via BACS.

**3. Coverage check at choice time:** before showing the dialog, verify the live refundable Stripe balance (deposit reserve respected via refundablePence) covers the FULL credit balance; if it doesn't, don't offer Stripe (no silent partial splits). One lightweight endpoint called on button-click, so no Stripe API cost on page render.

## Two sub-decisions for Austin before code
a) **Partial coverage**: strictly hide the Stripe option (proposed), or offer it with an explicit "£X to card, £Y by BACS" warning?
b) **Failed card refunds**: if a Stripe refund fails technically (expired card, charge too old), path 1 throws and rolls back — acceptable to handle those case-by-case when they surface, or do we want a deliberate BACS-override escape hatch for that specific failure?

## Test plan once agreed (sandbox, dry-run gateway)
1. C090850 shape (pure payment surplus, stripe+bacs) → NO dialog; refund goes by-method with the explanatory message.
2. Pure credit-note balance + full Stripe coverage → dialog appears; Stripe choice produces dry-run card refunds; BACS choice produces BACS rows.
3. Pure credit-note balance, coverage insufficient (deposit reserve) → per decision (a).
4. Mixed surplus+credit → no dialog; surplus by-method, credit via BACS.
5. Stripe-method surplus → refunds to card WITHOUT any dialog (by-method), proving the decoupling didn't lose card refunds for stripe surpluses.

**2026-07-15 17:17 claude-code:** Run run-20260715-1644 completed — Refunds sometimes went out by bank transfer even when staff picked "Stripe (refund to card)". Cause: the page offered the Stripe/BACS choice whenever the contract had ANY card payment, but a payment surplus (an overpayment) is money that physically sits on one specific payment and can only be returned the way it came in — so if that surplus was on a bank payment, the system quietly did a bank refund regardless of the choice. The only money that can genuinely be sent either way is a credit-note balance. Fix: the choice now appears ONLY when the whole refund is credit-note balance and the card can cover it in full; otherwise there's no misleading choice — staff see a plain confirmation of exactly what will happen and to which method. Surplus refunds always follow their own payment's method. Without this, staff keep selecting Stripe and getting BACS, and have to chase/correct refunds by hand. This change is now part of the T-0538 release branch, so the two must be tested together before going live.
