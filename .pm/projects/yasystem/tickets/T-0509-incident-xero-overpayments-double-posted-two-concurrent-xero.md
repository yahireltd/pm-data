---
id: T-0509
title: "INCIDENT: Xero overpayments double-posted — two concurrent xero/run-post executions (2026-07-02 09:27–09:38)"
type: incident
state: triaged
created: 2026-07-02T14:48:35Z
updated: 2026-07-02T16:38:16Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p0
reporter:
  kind: human
  name: Austin
  channel: accounts flagged doubled transactions
assignee: null
acceptance_criteria:
  - All duplicate overpayments removed from Xero or handed to accounts with a reviewed exception list
  - Local xeroOverpaymentID points at the surviving copy for every affected payment/deposit
  - Concurrent xero/run-post executions are impossible (mutex) and the UI prevents double submission
out_of_scope: []
code_anchors:
  - path: backend/controllers/XeroController.php
    symbol: actionRunPost / actionPost
  - path: backend/views/xero/post.php
  - path: common/services/XeroPostingService.php
    symbol: postOverpayments / postAllForPeriod
  - path: common/models/XeroFunctionsNew.php
    symbol: postOverpayments :595-649
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - xero
  - accounts
  - incident
attention: null
version: 4
---

## What happened
Accounts found doubled transactions in Xero. Every overpayment posted in the 2026-07-02 morning run exists TWICE in Xero (two different Xero UUIDs, same PaymentID reference) — e.g. Payments 48587 (£1,175.34, ad-house 88729) and 48594 (£10,837.24, OLAYIWOLA OGINNI 18776) each have two overpayment transactions.

## Root cause (verified in code + xero_posting_logs)
TWO concurrent executions of `XeroPostingService::postAllForPeriod()` ran overlapped:
- Run A started ~09:27:30 (token refresh, customers 09:27:44).
- Run B started ~30-45s later (customers pass 09:28:14 — only customer 89428 logged because run A had already stamped everyone else's xeroContactID).

Trigger: the `xero/post` page. After submitting, the form STAYS on screen and the page fires a long-running background GET to `xero/run-post`. Pressing "Run postAll" again (or refreshing/resubmitting) starts a SECOND full run; the first keeps going server-side (`ignore_user_abort(true)`, `set_time_limit(0)`). **There is no server-side mutex, no button disabling, and no already-running check.**

Why only overpayments doubled:
- Invoices: run B's batch rejected by Xero — "Invoice # must be unique" (09:30:25). Credit notes likewise (09:31:05). Xero's own number-uniqueness saved them.
- **Overpayments have no unique number in Xero.** Both runs took their work-list snapshot (`Payments::find()->where(['xeroOverpaymentID' => null])`) before either had written IDs back — the whole batch is sent in ONE `createBankTransactions` call and IDs are only saved after it returns, so a 1-second overlap doubles the entire batch. Same for `deposit_only_overpayment` (Deposits) at 09:33:25/26.
- The Xero idempotency key is `uniqid('yh_bt_op_', true)` — random per call, so Xero's idempotency protection cannot catch a second run.
- Allocation phase: most of run B's allocations were rejected by Xero ("already been paid off" / "duplicate application"), BUT allocations landed against a MIX of run-A and run-B overpayment copies (e.g. invoice_payment 49431 allocated against run B's copy of 48587). Cleanup cannot blindly delete run B's copies.

## Impact
- ~200+ duplicate overpayments (payments 48587–48824 range) + duplicated deposit-only overpayments — each duplicate inflates the customer's credit balance in Xero.
- Local DB `xeroOverpaymentID` points at whichever copy saved last; the twin is an orphan in Xero (mostly unallocated).

## Remediation plan
1. Enumerate pairs from xero_posting_logs (two success rows per model_id in the window) — sizing SQL in ticket conversation.
2. Cleanup script (dry-run first): for each pair, GET both overpayments from Xero; keep the copy with allocations (re-point local xeroOverpaymentID at it), delete the unallocated twin (RECEIVE-OVERPAYMENT bank transaction → status DELETED via BankTransactions endpoint); pairs where BOTH have allocations → manual review list for accounts.
3. Prevent recurrence: MysqlMutex around run-post (second trigger returns "already running" instead of running), disable the submit button in the UI, show run-in-progress state.

## Test plan (for the guard fix, once implemented)
- Submit Run postAll, immediately press it again → second attempt shows "a posting run is already in progress", logs show ONE stream only.
- Confirm a normal single run still completes and the button re-enables/next-day run works.

## Conversation

**2026-07-02 15:02 claude-code:** **Damage confirmed and repair tooling built (working tree on master, NOT yet committed — awaiting Austin's approval).**

**Scale:** 232 duplicate pairs confirmed from xero_posting_logs — 199 payment overpayments (48587–48824) + 33 deposit-only overpayments.

**Austin's key concern, addressed as the core design rule:** allocations were made against a MIX of first-run and second-run copies (e.g. payment 48587's allocation went to the second copy bb9c2fbb…), and local `xeroOverpaymentID` holds whichever batch saved last. So neither "delete the second UUID" nor "trust the DB" is safe. The tool trusts only Xero:

1. For every pair it GETs BOTH copies from Xero and reads status/total/remaining/allocations.
2. The copy WITH allocations is the keeper. The local record (payments/deposits + any linked mirror deposit rows) is repointed at the keeper BEFORE any deletion.
3. A twin is deleted only if Xero confirms zero allocations and full credit remaining — re-verified in the same request that deletes it (RECEIVE-OVERPAYMENT bank transaction → status DELETED, located via the `PaymentID:`/`DepositID:` reference we stamp at creation).
4. If BOTH copies carry allocations → MANUAL_REVIEW, nothing touched, accounts must unallocate one in Xero first.
5. Every repoint/delete is written to xero_posting_logs (`*_dedupe` model types) for audit.

**New files/changes:**
- `common/services/XeroOverpaymentDedupeService.php` — the logic above.
- `XeroController::actionDedupeOverpayments` — browser-run (Xero token is session-bound), plain-text report. **Dry-run by default**; `&execute=1` applies; `&limit=N` for a trial batch.
- `XeroController::actionRunPost` — MySQL GET_LOCK concurrency guard: a second trigger returns "already in progress" and logs the blocked attempt (connection-scoped lock, self-releasing on crash).
- `backend/views/xero/post.php` — submit button disables on click; JS now surfaces the "already running" state instead of falsely reporting "Posting complete".

**Run procedure (after deploy):**
1. Log into backend with a Xero-connected session → `/xero/dedupe-overpayments` (dry-run) → review the full report with accounts, especially any MANUAL_REVIEW rows.
2. Trial: `/xero/dedupe-overpayments?limit=5&execute=1` → verify in Xero UI the 5 customers now show one overpayment each and invoices remain paid.
3. Full run: `/xero/dedupe-overpayments?execute=1` (~10-15 min, rate-limit aware).
4. Re-run with no params — expect every row ALREADY_CLEAN.
5. Guard test: start a posting run, click Run postAll again in a second tab → "already in progress".

**2026-07-02 16:35 claude-code:** **Status 2026-07-02 17:45 — cleanup BLOCKED until tomorrow: Xero daily API cap exhausted.**

The dry run reported `Xero rate-limited — waiting 71468s` (~19.9h) — that's the DAILY cap (5,000 calls per rolling 24h), spent by the morning double-run plus today's diagnostic attempts. Quota frees as the morning incident's calls age out; Xero's own Retry-After puts the resume at **~13:30 Thu 3 July** (some may free earlier as the ~09:30 burst rolls off).

**Deployment learnings baked in along the way (server runs Apache + php-fpm 8.3):**
- HTTP streaming of the report is impossible on this stack (proxy/gateway timeout at ~60-100s, mod_deflate + FPM buffering, `apache_setenv` unavailable) → report now written progressively to `@runtime/xero-dedupe-<ts>-<mode>.txt`, watched live via `/xero/dedupe-report` (auto-refresh viewer).
- Abandoned browser requests keep running server-side (`ignore_user_abort`) — a zombie run held the report file open in a silent 20h rate-limit sleep (confirmed via lsof). Cleared with php-fpm restart.
- `XeroRateLimitHelper` (trait) gained an optional `rateLimitNotifier` hook (null default — zero behaviour change for posting flows) so back-off sleeps are announced in the report.
- Dedupe now self-paces under 55 calls/min (`throttle()`), and ABORTS with a clear "resume after <time>" message when Retry-After exceeds 10 min (daily cap) instead of sleeping for hours inside an invisible worker. Partial progress is safe — re-running skips ALREADY_CLEAN pairs.

**Plan for Thu 3 July (quota budgeting — total 5,000/day):**
1. Do NOT run anything Xero-related before the cleanup except essentials.
2. ~13:30 (or try ~10:00 — may work once the 09:30 burst ages out): full dry run (~470 calls), review with accounts (esp. MANUAL_REVIEW rows).
3. `?limit=5&execute=1` trial (~25 calls) → verify in Xero UI.
4. Full `?execute=1` (~700-900 calls) → re-run dry (~470) → expect ALREADY_CLEAN.
5. Normal daily posting run AFTER the cleanup, same day is fine (~4,000 calls of headroom remain).
6. Accounts told not to trigger Xero posting today.

**2026-07-02 16:38 claude-code:** **Daily-cap diagnosis CONFIRMED by Xero's own headers** via the new `/xero/quota` gauge (2026-07-02 ~17:50):

```
Day limit remaining:    0 (of 5000 / rolling 24h)
Minute limit remaining: 60 (of 60)
Retry-After:            71202 seconds
```

Minute limit is full — purely the daily cap. Reset ~13:25 Thu 3 July (rolling window; the morning incident's burst ages out from ~09:30, so check /xero/quota mid-morning — it may free up earlier).

Where the budget went: double-run allocation phases are one call per allocation (both runs paid them), plus TWO dedupe dry-runs (~470 calls each) that ran invisibly to completion server-side after their browser pages timed out (ignore_user_abort), plus unthrottled retry bursts.

**Thu 3 July runbook (updated):**
1. Morning: check `/xero/quota`. Proceed when Day-limit-remaining comfortably exceeds ~1,500.
2. php-fpm restart done today to clear the zombie run (otherwise it would wake at reset and burn ~470 calls on old code).
3. Dry run → review with accounts → `?limit=5&execute=1` trial → verify in Xero UI → full `?execute=1` → re-run dry expecting ALREADY_CLEAN.
4. Normal daily posting AFTER cleanup. Accounts must not trigger anything Xero-side before then.
