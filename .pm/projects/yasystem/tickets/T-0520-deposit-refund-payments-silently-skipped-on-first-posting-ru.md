---
id: T-0520
title: Deposit refund payments silently skipped on first posting run (stale $newRows after credit-note auto-post)
type: bug
state: triaged
created: 2026-07-07T12:57:51Z
updated: 2026-07-07T12:57:51Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: accounts found many unposted deposit refund payments
assignee: null
acceptance_criteria:
  - A deposit refund created today posts credit note AND refund payment in the same posting run
  - "No silent skips: every selected allocation ends the run with either xeroPaymentID set or an error log row"
  - Backlog of stranded allocations reconciled with accounts (manually-fixed marked, missing ones re-posted)
out_of_scope: []
code_anchors:
  - path: common/services/XeroPostingService.php
    symbol: postDepositRefunds :1213-1240
  - path: common/models/XeroFunctionsNew.php
    symbol: postDepositRefunds :1499-1530
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - xero
  - accounts
attention: null
version: 1
---

## Problem
New-ledger deposit refunds post their CREDIT NOTE to Xero but not the refund PAYMENT against it. Accounts has found many cases; bookkeepers have been entering the missing refund payments manually in the Xero UI (e.g. deposit 32376 / CR 74738: credit note posted via API 5 Jun 12:37, refund payment entered by hand by "Book keeper2" on 10 Jun).

## Root cause (XeroPostingService::postDepositRefunds)
The run auto-posts missing credit notes FIRST, then re-queries the allocations "after posting credit-notes" into `$rows` — **but the payment loop iterates the stale pre-refresh `$newRows`**, whose eager-loaded `creditNote` relations still show `xeroInvoiceID = NULL` for the credit notes posted seconds earlier in the same run. The guard `if (!$r->creditNote->xeroInvoiceID) continue;` therefore silently skips them — no payment, no error, no log row.

Because the credit note and the allocation are almost always created at the same moment, they always share the same posting window → **nearly every new-ledger deposit refund is skipped on its first (usually only) posting run**. It only self-heals if the same date window happens to be re-run later. The legacy path (`postLegacyDepositRefunds`) re-filters after its refresh and is NOT affected.

## Fix
One line after the refresh: `$newRows = array_filter($rows, fn($r) => $r->contract && $r->contract->usesNewAccounting());` — rebuilds the loop's list from the refreshed rows. Applied with an explanatory comment.

## Backlog sweep (coordinate with accounts BEFORE re-posting)
Stranded rows: `SELECT ... FROM deposit_refund_allocations WHERE xeroPaymentID IS NULL AND creditNoteID IS NOT NULL` (join ya_contracts for contractNo, invoices for creditNote xeroInvoiceID). The list splits into:
- **Manually fixed in Xero already** (like deposit 32376) — re-posting will be rejected by Xero ("no credit remaining"), harmless but noisy; better to reconcile these in the DB (set xeroPaymentID from the manual payment or mark them) than to re-post.
- **Genuinely missing** — re-run per window with `/xero/post-deposit-refunds?date=YYYY-MM-DD` (only selects xeroPaymentID IS NULL, so safe against double-posting).

## Test plan
1. Create a deposit refund in the system (credit note + allocation created together, as normal).
2. Run the posting for that date window ONCE. Verify in one run: credit note lands on Xero AND the refund payment lands against it (xero_posting_logs shows `auto_post_deposit_itemised_creditnotes_before_refunds` followed by `deposit_refund … success`, and `deposit_refund_allocations.xeroPaymentID` is populated).
3. Re-run the same window → allocation not re-selected (xeroPaymentID set), no duplicate payment.
4. Edge: a refund whose credit note was ALREADY on Xero before the run still posts as before.
5. Cross-impact: legacy deposit refunds (`postLegacyDepositRefunds`) unchanged — spot-check one.