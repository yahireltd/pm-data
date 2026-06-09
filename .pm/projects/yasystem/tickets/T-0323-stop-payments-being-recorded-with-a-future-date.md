---
id: T-0323
title: Stop payments being recorded with a future date
type: bug
state: triaged
created: 2026-06-09T19:21:45Z
updated: 2026-06-09T19:53:59Z
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
version: 3
backlog_status: confirmed_for_release
estimated_effort: S
source: discovered
---

## Problem

The manual payment form lets staff date a payment in the future. This was the root trigger of the C090586 over-refund: a BACS receipt was keyed in on 4th May but dated 5th May. For the rest of that day the system's "unused payment" calculation could not see the payment's allocation (it ignores future-dated records), so when the statement page was viewed it allocated the same money to the invoice a second time. That phantom allocation eventually caused a £1,255.85 wrong Stripe refund three weeks later.

## Build order

No code dependencies — can be built any time, including BEFORE the sprint as a quick risk reducer. Gated on one business answer (below).

## Gate: business question first

Ask whoever keys in BACS receipts (likely via accounts manager): "When you enter a payment, do you ever deliberately date it tomorrow or later — e.g. the date you expect it to clear?" Record the answer here.
- If NO (expected): proceed with the validation below.
- If YES: do NOT build this; the fix shifts entirely to T-0326 (the calculation blind spot) and this ticket is closed as won't-do with the reason recorded.

## Context

Forensically confirmed via the invoice_payments ID bracket: rows stamped 05-05 were physically created on 04-05 (a batch of ~14 BACS receipts entered 08:10–08:36, some backdated to 01-05, three post-dated to 05-05). Backdating (entering a payment with a past date) must continue to work — it is core to how accounts record bank receipts.

## Implementation outline

One validation rule on the manual payment form's date field: not after today (server-side rule, not just browser-side). The same date feeds both the payment amount and the deposit amount on that form, so one rule covers both.

## Testing plan

**On the test box (sandbox):**

1. **Rejected:** submit the form with tomorrow's date → clear validation message, nothing saved (no payment row, no deposit row, no allocation rows — verify by row counts).
2. **Accepted:** today's date → saves and allocates exactly as before. Yesterday and last week → same. Compare the created allocation rows for a backdated payment against the same entry made before the change (the nightly copy gives you an untouched baseline each morning).
3. **Both amount fields:** future date rejected when only a payment amount is entered, when only a deposit amount is entered, and when both are.
4. **Browser bypass:** POST the form payload directly with a future date (curl) — the server-side rule must reject it; this cannot be a date-picker-only restriction.
5. **Cross-impact sweep:** the manual payment form is one entry point. Confirm (read-only review, results recorded here) which other paths set a payment 'created' date — Stripe webhook payments (always now — unaffected), any admin CRUD screens for payments, any import/console jobs. Decide per path whether the same rule applies; do not silently change them in this ticket.
6. **Time-zone edge:** at 23:55 local, "today" must still be accepted — confirm the comparison uses Europe/London, matching how the form composes the date with the current clock time.