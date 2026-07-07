---
id: T-0520
title: Deposit refund payments silently skipped on first posting run (stale $newRows after credit-note auto-post)
type: bug
state: triaged
created: 2026-07-07T12:57:51Z
updated: 2026-07-07T14:56:08Z
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
version: 4
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

## Conversation

**2026-07-07 13:11 claude-code:** **Root-cause timeline (from git archaeology, confirmed with Austin):**

1. **1–3 Jul 2025** — latent bug introduced: `b3b02d2d` created the `$newRows` filtered list; `0113f46d` added auto-post-credit-notes-first + a re-query into `$rows` but the payment loop kept iterating stale `$newRows`. The refresh was added to solve exactly this scenario — wrong variable. Mostly harmless while full runs posted credit notes at step 4 before step 13.
2. **23 Apr 2026** — PHP 7.4→8 upgrade (`5ab7fa71`) brought a newer Xero SDK where `idempotency_key` became argument 3; calls passing the old arg order were rejected by Xero. Fixes landed piecemeal: `8adcb891` (6 May, createPayments/createCreditNoteAllocation) and `3895a7a3` (20 May, createBankTransactions). During the partially-broken window, credit notes and payments stopped landing in the same run → credit notes got posted via the auto-post-first block → the latent bug fired on nearly every deposit refund.
3. Stranded eras in `deposit_refund_allocations` (xeroPaymentID NULL): bulk **11 May–12 Jun** (bug era; ~300 rows, credit note on Xero, payment missing); **13–28 Jun clean** (fixes fully deployed, runs completing); **29 Jun+** nothing posted (posting freeze from T-0509 incident + quota lockout — pure backlog, no bug).
4. Bookkeepers have been manually entering missing refund payments in Xero through the bug era — the backlog sweep must reconcile against their manual entries (re-posts of those will be rejected with "no credit remaining", harmless but noisy).

**Also found:** composer.json/lock still pin xeroapi/xero-php-oauth2 2.23.3 while the deployed vendor has the new-signature SDK — vendor was updated outside the lockfile during the PHP 8 work. A future `composer install` from the lock could reintroduce the old SDK. Recommend aligning the lock with the installed version (separate chore).

**2026-07-07 14:07 claude-code:** **ROOT CAUSE CORRECTION — the stale-$newRows bug is real but DORMANT; it did not cause the strandings.** Log evidence (Austin's pulls):
- `auto_post_deposit_itemised_creditnotes_before_refunds`: 0 rows ever → the auto-post-first doorway never opened → the stale-list skip never executed. (Fix stays — it's correct defensive code — but it isn't the culprit.)
- `all_deposit_refunds` batch errors: 0 rows → the whole-batch failure path never fired either.
- The real trace: **474 per-row `deposit_refund` errors, all identical — "Payments can only be made against Authorised documents; Payment amount exceeds the amount outstanding on this document" — from 2026-04-16 to 2026-07-06** (vs 5,372 successes). 0 write-back warnings.

**Working theory (being confirmed by spot-checks):** a RACE between posting lag and the bookkeeper's bank reconciliation. Refund money leaves the bank on day 0; our posting run for that window arrives days/weeks later (e.g. CR 74738: refund 25 May, CN posted 5 Jun). In between the bookkeeper must reconcile the bank-feed refund line and creates the payment manually in Xero — so when our delayed run finally attempts the payment, the credit note has nothing outstanding → per-row reject. Her manual reconciliation is the CAUSE of the rejects, and the posting lag is the cause of her manual reconciliation. Likely became systematic from mid-April when the PHP8/SDK breakage disrupted posting and pushed her into the manual habit. Historical "gap" rows back to Jul 2025 show occasional early strandings rescued by window re-runs. Secondary quiet path: step-13 dying at API setup is caught by postAllForPeriod and logged only to the Yii app log — zero posting-log trace.

**Remedy package (extends the ticket scope):**
1. Self-healing selector: drop the lower `created` bound in postDepositRefunds (+ legacy) — `xeroPaymentID IS NULL` already guarantees idempotency; every run then retries all stranded rows.
2. Per-row error logging in the batch-failure catch (no more model_id=0-only traces).
3. Kill the lag: daily posting cadence (the race only exists because posting trails refunds by days).
4. Backlog: rows the bookkeeper already paid manually must be reconciled in the DB (record her payment), NOT re-posted into guaranteed rejects.
5. Workflow agreement with bookkeeper: after daily posting is live, wait 24h before manually reconciling refund lines.

**2026-07-07 14:56 claude-code:** **Hardening package built (items 1-4, working tree on master, awaiting Austin's commit).** Per Austin's decision: date-range selection is RESPECTED everywhere — no automatic lookback; recovery stays a deliberate, human-chosen range re-run. What changed:

1. **Per-row tolerance** (`postDepositRefunds`): the credit-note pre-check and helper-row build now guard every relation (creditNote/depositRefund/customer/deposit) and wrap each row in try/catch — a broken row logs a per-row `deposit_refund` error and is skipped. Under PHP 8, one null relation used to be a fatal Error that killed the ENTIRE step for the window with zero posting-log trace. Legacy path already had null-safe filters.
2. **Bank-account fetches wrapped + cached**: new `fetchBankAccount()` (retry via the rate-limit trait, per-request cache) replaces 9 sites × 2 raw `getAccount()` calls — a transient 429 at step start can no longer abort a step, and each full run saves ~16 duplicate API calls. Service now composes `XeroRateLimitHelper` (avoids the deprecated static-on-trait call).
3. **Run/step logging**: `postAllForPeriod` rewritten table-driven (16 identical try/catch blocks → one loop; also fixes a latent bug where step 11's error was written to a misspelled summary key). Every run logs `posting_run start/finish` with its window; every step logs a `posting_step` row — compact summary on success, exception class+message on error. Silent step death is now impossible.
4. **`/xero/unposted-report`** (`?olderThanDays=3`): zero-API, zero-session HTML report counting unposted items per document type (invoices, credit notes, payment/deposit overpayments, invoice allocations, deposit refunds new+legacy) with oldest/newest dates and value totals, red-flagging non-zero rows. This is the noticing mechanism; the fixing mechanism remains a deliberate range re-run.

## Test plan
1. Run a posting for a small recent range → xero_posting_logs shows posting_run start, 16 posting_step rows, posting_run finish; summaries humanly readable.
2. `/xero/unposted-report` loads instantly with plausible counts (deposit refunds new-ledger should show the known ~300 backlog until reconciled).
3. Temporarily break one allocation's creditNoteID on TEST → run posting → that row logs a per-row error, all other refunds in the window still post.
4. Cross-impact: single-step actions (post-deposit-refunds?date=) unchanged; summary array keys unchanged for anything consuming run-post's JSON.
