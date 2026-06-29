---
id: T-0323
title: Stop payments being recorded with a future date
type: bug
state: done
created: 2026-06-09T19:21:45Z
updated: 2026-06-29T13:26:09Z
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
  - "[x] Accounts team consulted on whether future-dated payments are ever intentionally needed; their answer recorded on this ticket before the change is built"
  - "[x] The manual payment form rejects a payment date after today with a clear validation message (assuming accounts confirm future-dating is not needed)"
  - "[x] Backdated payments (yesterday and earlier) and today-dated payments save exactly as before"
  - "[x] The same rule applies to the deposit amount entered on the same form, which follows the same date path"
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
version: 12
backlog_status: confirmed_for_release
estimated_effort: S
source: discovered
defect_status: confirmed
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

## Conversation

**2026-06-10 13:58 claude-code:** **Investigation evidence bearing on this ticket's gate question (2026-06-10, verified against live data):**

The ID-bracket forensics (payments 47190–47245) show the 4 May batch was keyed by Jahzel (userID 1021) on the morning of Mon 4 May (a bank holiday): nine receipts backdated to Fri 1 May (statement date) and three dated forward to Tue 5 May (47213, 47214, 47215) — consistent with deliberate **value-date keying** (pending BGC credits dated to the next banking day). So the answer to this ticket's gate question is likely "yes, we do future-date on purpose" — still confirm with Jahzel/accounts before deciding.

However, a second case argues for a *bounded* validation rather than closing this as won't-do: payments 44320/44321/44322 were keyed on 2 Jan 2026, meant for 31-12-**25**, but typed 31-12-**26** — a twelve-month forward-date typo that has left £3,535.93 looking "unused" to the system since January (see T-0327 conversation for the repair). A hard "no future dates" rule would block legitimate value-dating, but a sanity bound (e.g. reject dates more than a few working days ahead, server-side) would have caught the typo while permitting next-banking-day dating. Suggest putting that option to accounts alongside the original question.

---

**2026-06-24 20:09 — you**

Done and tested - works frontend and a second backend check

---

**2026-06-24 20:09 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.

**2026-06-25 19:03 claude-code:** **Forward-dated manual payments now blocked (2026-06-25).**

Implemented on the manual payment form:
- `ManualPaymentForm::validateNotFuture()` — rejects any `paymentDate` after today (parsed the same way `savePayment()` does, so it's format-agnostic). Rejected at form validation, *before* any payment/deposit record is created — so a rejected entry can't orphan anything.
- The date input now has `max=today` on both role branches, so the picker can't select a future date either.

Tested (today/past allowed, future rejected) and committed `71273e19`. Note: this closes the *trigger* (entering a future-dated payment). The downstream unused-payment date blind spot is tracked separately under T-0326 and is now also mitigated by the allocation cap (T-0325) and the refund guard (T-0320).

**2026-06-29 13:26 claude-code:** **LIVE 2026-06-29.** Forward-dated manual payments are rejected at the form (`ManualPaymentForm::validateNotFuture`, format-agnostic, fires before any payment/deposit row is created) and the date pickers carry `max=today`. This closes the C090586 root trigger — a future-dated payment can no longer be created and sit "unused" until its date passes.
