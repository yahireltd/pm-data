---
id: T-0327
title: Repair contract C090586 and reconcile the customer over-refund
type: support
state: triaged
created: 2026-06-09T19:25:57Z
updated: 2026-06-09T19:55:30Z
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
version: 3
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