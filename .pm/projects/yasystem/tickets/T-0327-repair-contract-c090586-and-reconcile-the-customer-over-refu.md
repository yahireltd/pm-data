---
id: T-0327
title: Repair contract C090586 and reconcile the customer over-refund
type: support
state: triaged
created: 2026-06-09T19:25:57Z
updated: 2026-06-09T19:27:49Z
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
version: 2
backlog_status: confirmed_for_release
estimated_effort: S
source: discovered
---

## Problem

The customer on contract C090586 was due a £138 refund but the system sent £1,255.85 back to their card through Stripe, and recorded a further £6 and £138 as bank-transfer refunds. The customer honestly flagged the overpayment. The contract record now shows £1,261.85 outstanding, and the underlying data still contains the phantom allocation that caused the whole incident.

## What needs to happen

1. Accounts confirm whether the £6 and £138 bank transfers (references BR91febc3 and BRd29a371) were actually sent. Recovery amount is £1,261.85 if they were, £1,117.85 if not.
2. Agree the recovery with the customer (new payment link or bank transfer — a Stripe refund cannot be pulled back).
3. Correct the records: the phantom allocation (invoice payment row 48031, £1,261.85 applied from payment 47215 which had nothing left) needs an agreed repair so the contract statement reflects reality. Record the before/after state on this ticket. Do not delete rows ad hoc — refund rows have credit-note allocations hanging off them.
4. Re-run the corruption detection query across all contracts and attach the result (earlier run found only this contract plus one early-system contract — confirm and decide on the old one).

## Investigation reference

Full forensic chain established 2026-06-09: payment entered 04-05 post-dated to 05-05 → unused-payment calculation blind to the future-dated allocation → statement page view created phantom second allocation → invoice looked fully paid → customer's genuine £1,255.85 Stripe payment applied as £0.00 → refund process treated it as a refundable overpayment.