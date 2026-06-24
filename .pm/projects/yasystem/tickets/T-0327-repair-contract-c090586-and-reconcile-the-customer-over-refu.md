---
id: T-0327
title: Repair contract C090586 and reconcile the customer over-refund
type: support
state: done
created: 2026-06-09T19:25:57Z
updated: 2026-06-24T20:10:59Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: customer
  name: Eugenie Ellor (via accounts)
  channel: customer-contact
assignee: null
acceptance_criteria:
  - Accounts have confirmed whether the £6 and £138 BACS refund transfers physically left the bank; the answer and final recovery amount are recorded on this ticket
  - The recovery amount has been requested from and settled by the customer (or a write-off decision is recorded with sign-off)
  - The phantom allocation is corrected with the repair method, before/after row values, and date recorded on this ticket
  - After repair, the contract statement shows the true position and the contract balance agrees with the bank reality
  - The corruption detection query has been re-run across all contracts with the results attached, and a decision is recorded for the one historic early-system contract
out_of_scope:
  - Code changes (covered by the other sprint tickets)
code_anchors:
  - path: common/models/Payments.php
    symbol: getUnusedPayment
    lines: 148-218
  - path: common/models/Invoices.php
    symbol: applyInvoicePayment
    lines: 343-370
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
  - data-repair
attention: null
version: 5
backlog_status: confirmed_for_release
estimated_effort: S
source: discovered
---

## Problem

The customer on contract C090586 was due a £138 refund but the system sent £1,255.85 back to their card through Stripe, and recorded a further £6 and £138 as bank-transfer refunds. The customer honestly flagged the overpayment. The contract record now shows £1,261.85 outstanding, and the underlying data still contains the phantom allocation that caused the whole incident.

## Build order

Independent of the code tickets — can run in parallel as an operations task. The data repair should be REHEARSED on the test box first (recipe below); the live repair is a single reviewed script run.

## What needs to happen

1. **Bank facts (accounts):** confirm whether the £6 and £138 bank transfers (references BR91febc3 and BRd29a371) were actually sent. Recovery amount is £1,261.85 if they were, £1,117.85 if not. Record the answer here.
2. **Customer recovery:** agree the amount with the customer (new payment link or bank transfer — a Stripe refund cannot be pulled back). Record outcome (or write-off sign-off) here.
3. **Xero check (gates the repair method):** establish whether May's figures including this contract have already been posted/reconciled in Xero. If YES → prefer a compensating entry dated today (audit-friendly, period totals untouched). If NO → correcting the phantom row directly is acceptable. Record the decision and reasoning here.
4. **Data repair** (the phantom is invoice-payment row 48031: £1,261.85 applied from payment 47215, which was already fully consumed by row 48030).
5. **Fleet check:** re-run the corruption detection query across all contracts and attach results; decide what to do about the one historic early-system contract.

## Testing plan / rehearsal

**On the test box (nightly copy contains the corrupted state):**

1. Write the repair as a single reviewed SQL script (or console command) with a matching capture query that snapshots the affected rows BEFORE and AFTER (invoice payments, payment refunds, credit-note allocations, contract balance fields).
2. Run it on the test box. Then verify, on screen, the contract statement: payments, refunds, credits and the final balance must reconcile with the agreed bank reality (depends on step 1's answer). Check the customer's OTHER contracts are untouched.
3. Check downstream surfaces on the test box: unpaid-invoices list, customer area statement, and a dry-run of the Xero path for the affected period — confirm the repair does not resurface the contract anywhere it should not.
4. Confirm the refund screen for this contract no longer offers a phantom refund (the Process Payment Refunds button should only appear if a genuine balance remains).
5. Only after a clean rehearsal: run the same script on live inside a transaction, with the before/after capture output saved to this ticket. The nightly copy means a botched live repair can be compared against the previous day's data, but treat the rehearsal as the real safety net.

**Detection query for step 5 (fleet check):** payments where the sum of allocation rows exceeds the payment amount by more than a penny — group invoice_payments by paymentID, having SUM(amountApplied) > payments.amount + 0.01; join to contracts for context. Attach the full result set here.

## Conversation

**2026-06-10 13:55 claude-code:** **Findings from the 2026-06-10 data verification (all queries in repo file `sql/incident-c090586-proof-queries.sql`):**

**Exact row inventory for the C090586 repair** — contract `ya_contracts.id` 46518, customer 24054:
- Phantom allocation: `invoice_payments` 48031 (£1,261.85, payment 47215 → invoice 73100), stamped 2026-05-05 08:36:00, physically inserted 4 May between 08:36 and ~09:00 (proven by ID bracket: it precedes row 48032, a Stripe allocation wall-clocked 04-05 ~09:00).
- Refund artefacts 25-05 14:38, all by userID 1021 (Jahzel): `payment_refunds` **3140** (£1,255.85 Stripe, `stripe_refunds` 35613 = re_3TWFmuKYqp01XEip1TcPpcgM, amount stored as 125585 pence, linked `payment_refund_overpayments` row **352**), **3141** (£6.00, ref BR91febc3, `credit_note_allocations` 8728 → CR 73371), **3142** (£138.00, ref BRd29a371, cna 8729 → CR 74839).
- No credit-note double-dipping: cnas 8728/8729 are refund-backed only (invoiceID NULL).

**Xero gate answered:** the phantom 48031 was **never accepted by Xero** — posting rejected 2026-05-20 18:26:33 and again 2026-06-05 11:23:47 ("Overpayment cannot be allocated as the allocation amount is greater than the amount remaining on the Overpayment", `xero_posting_logs` 70622/71029, still retrying). Payment 47215 + row 48030 posted fine on 20-05. So correcting 48031 locally **converges** with Xero. Separately, Xero now holds an unallocated £1,255.85 overpayment for payment 47445 (posted 2026-06-05, log 71823) that needs a Xero-side correction after the refund reconciliation.

**Fleet check results:** over-allocation sweep returns only 47215 and one historic case (payment 2493, £1,836.97 over, July 2021 — the known early-system contract).

**Three additional repairs to fold into this ticket** — payments 44320 (£2,673.01, C086707), 44321 (£491.77, C087714), 44322 (£371.15) and their allocations `invoice_payments` 45027/45028/45029: keyed by Jahzel on 2 Jan 2026 07:13–07:15 but dated 31-12-**2026** (year typo for 31-12-2025, proven by ID bracket — the batch sits between Stripe rows of 1 Jan and 2 Jan 2026). Until 31-12-26 these payments look 100% unused to `getUnusedPayment` (£3,535.93 total). They are NOT over-allocated today (their invoices were settled exactly at entry, so the page-view allocator's `balance > 0` gate never fires), but any new invoice on those contracts arms the C090586 mechanism instantly. Xero has NOT been fed phantom overpayments for them (no `overpayment` rows in xero_posting_logs for these payment ids). Repair: shift `created` back one year on all six rows, preserving the +1-minute convention on the allocation rows; rehearse on the test box first like the main repair.

---

**2026-06-24 20:10 — you**

This was the contract that flagged the issue. I have been through it again  reset the records back to how it was at the time of refund (before and after fix) before fix it did the same (replicated) after it refunded the expected amount

---

**2026-06-24 20:10 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
