---
id: T-0323
title: Stop payments being recorded with a future date
type: bug
state: triaged
created: 2026-06-09T19:21:45Z
updated: 2026-06-09T19:27:38Z
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
  - Accounts team consulted on whether future-dated payments are ever intentionally needed; their answer recorded on this ticket before the change is built
  - The manual payment form rejects a payment date after today with a clear validation message (assuming accounts confirm future-dating is not needed)
  - Backdated payments (yesterday and earlier) and today-dated payments save exactly as before
  - The same rule applies to the deposit amount entered on the same form, which follows the same date path
out_of_scope:
  - Fixing the unused-payment calculation's date blind spot itself (separate ticket)
  - Stripe-originated payments (always dated now)
code_anchors:
  - path: common/models/ManualPaymentForm.php
    symbol: rules
    lines: 21-44
  - path: common/models/Deposits.php
    symbol: recordPaymentOrDeposit
    lines: 99-304
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
version: 2
backlog_status: confirmed_for_release
estimated_effort: S
source: discovered
---

## Problem

The manual payment form lets staff date a payment in the future. This was the root trigger of the C090586 over-refund: a BACS receipt was keyed in on 4th May but dated 5th May. For the rest of that day the system's "unused payment" calculation could not see the payment's allocation (it ignores future-dated records), so when the statement page was viewed it allocated the same money to the invoice a second time. That phantom allocation eventually caused a £1,255.85 wrong Stripe refund three weeks later.

## Context

Forensically confirmed via the invoice_payments ID bracket: rows stamped 05-05 were physically created on 04-05 (a batch of ~14 BACS receipts entered 08:10–08:36, some backdated to 01-05, three post-dated to 05-05).

Before building: confirm with the accounts team whether dating a payment tomorrow is ever intentional (e.g. expected clearing date). If they need it, record the decision and solve the underlying calculation blind spot instead (see the getUnusedPayment ticket); if not, a simple validation rule kills this failure mode at the source.

Backdating (entering a payment with a past date) must continue to work — it is core to how accounts record bank receipts.