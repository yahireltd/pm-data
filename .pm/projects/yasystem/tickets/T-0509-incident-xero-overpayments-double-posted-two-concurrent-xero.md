---
id: T-0509
title: "INCIDENT: Xero overpayments double-posted — two concurrent xero/run-post executions (2026-07-02 09:27–09:38)"
type: incident
state: triaged
created: 2026-07-02T14:48:35Z
updated: 2026-07-09T13:57:49Z
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
version: 7
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

**2026-07-03 15:35 claude-code:** **API deletion is impossible — pivoting to a deep-linked manual-void checklist.**

Trial execute (5 pairs, 2026-07-03 16:31) confirmed: Xero rejects status=DELETED on RECEIVE-OVERPAYMENT bank transactions with a generic "An error occurred in Xero" ValidationException — even with the full transaction payload. This matches Xero's documented limitation: overpayment-type bank transactions cannot be updated/deleted via API, the Overpayments endpoint has no delete, and refunding via the Payments endpoint would create fake ledger movements in the bank accounts (unacceptable — these ARE the payment records: the system books every customer payment as a RECEIVE-OVERPAYMENT then allocates it to invoices, so each duplicate is a phantom payment receipt inflating the Xero bank balance and customer credit).

Good news from the trial: keeper/twin selection worked perfectly on all 5 pairs, and nothing was changed (delete failed cleanly, DB untouched because repoint only happens when the DB points at a twin).

**New flow:**
1. `/xero/dedupe-checklist` (read-only, ~700 API calls) → builds an HTML checklist at `/xero/dedupe-checklist-view`: one row per twin with customer, amount, date, reference and a deep link (`go.xero.com/Bank/ViewTransaction.aspx?bankTransactionID=…`) STRAIGHT TO THE TWIN — the kept copy is never linked, so accounts cannot void the wrong one. Both-allocated pairs are listed separately as DO-NOT-VOID manual review.
2. Accounts works the checklist in Xero UI (Options → Void/Delete per row, ~230 rows).
3. `/xero/dedupe-overpayments?execute=1` afterwards = verification pass: confirms one survivor per pair and repoints payments/deposits.xeroOverpaymentID to it (this DB half still works fine via API).

Progress for the checklist build is watchable at /xero/dedupe-report as usual.

**2026-07-03 16:14 claude-code:** **2026-07-03 ~16:50 — checklist build hit the daily cap again; hardening added.**

The build found **264 pairs** (the definitive count — the earlier 232 used a narrower log window that missed straggler re-posts up to 09:40). It completed allocation checks for most pairs, then exhausted the day's quota at 16:45 (yesterday evening's runs still in the rolling window + today's trials + the build itself). The second phase (bank-transaction link lookups) all failed on the empty quota, so that checklist HTML has no links — discard it.

**Hardening now in place:**
- Quota preflight on both tools: refuses to start when X-DayLimit-Remaining < what the run needs (~900 for checklist build, ~700 for execute); `&force=1` overrides.
- Link lookups moved inline per pair (3 calls/pair), with clean daily-cap abort → a cap hit now still yields a USABLE PARTIAL checklist; re-running later completes it (voided pairs drop out as ALREADY_CLEAN).

**Next window:** quota resumes ~13:24 Fri 4 Jul (rolling). Friday also needs the normal daily posting. Recommended: check /xero/quota Friday late afternoon; when remaining > ~1,500 run `/xero/dedupe-checklist` (≈15 min), review at `/xero/dedupe-checklist-view`, accounts voids the ~264 twins via the deep links (Fri evening / Monday), then `/xero/dedupe-overpayments?execute=1` as the final verify+repoint pass.

**2026-07-09 13:57 claude-code:** **Full verification of the 07-02 repair + discovery of a related invoice-orphaning incident (investigated 2026-07-09 with Austin).**

**1. The 07-02 duplicate-overpayment repair is CONFIRMED COMPLETE.** Log analysis (live `xero_posting_logs`, zero Xero API used): 235 duplicated `overpayment` + 33 `deposit_only_overpayment` objects; 231+33 = all 264 pairs had both copies inside 09:32–09:40 — exactly matching the manual-void checklist. Austin confirms he personally voided all 264. The 107 `payment_linked_deposit_overpayment` "duplicates" are the same Xero objects logged from the deposit side (5,368/5,368 GUID overlap with `overpayment`) — no separate repair was ever needed.

**2. Same root cause also orphaned itemised invoices (now healed).** The concurrent runs raced on invoices too: invoice numbers are unique in Xero, so instead of duplicating, the loser run got "Invoice # must be unique" and the winner's GUID write-back was lost → invoices existed in Xero but `xeroInvoiceID` stayed NULL locally, re-erroring every run and (since the July hardening) blocking payment/credit/deposit allocations. Two events: **2026-06-19** (Xero 500 mid-`createInvoices`, 289 invoices) and **2026-07-02** (this incident, 440 invoices). All **729 healed** on 2026-07-09 via new `/xero/heal-invoice-numbers` (log-driven, reads GUIDs back by invoice number, dry-run default, bulk chunked lookups; on master).

**3. Remaining repair worklist (small):**
- **`overpayment` 47708 — posted 4× on 2026-05-20 (deploy/test session), up to 3 surplus copies, OPEN period → needs voiding.** Keeper = GUID in `payments.xeroOverpaymentID`; echoes `invoice_payment` 48528 / `payment_linked` 32405 clear with it.
- Closed-period historical leaks (accounts' call, likely absorbed by reconciliation): overpayments 39540 (2025-07/08), 40424 (2025-09), 44413 (2026-01/02); plus ~22 payment-family duplicates from 2025-07→2026-03 (`credit_note_refund` ×7, `deposit_refund` ×10, `legacy_deposit_refund` ×4, `payment_refund_overpayment` 283 = a triple, `deposit_charge_allocation` 4481).

**4. Root cause is architectural, not incidental.** Every posting path = "select WHERE xero-id IS NULL → create in Xero → write id back after". Anything separating create from write-back (Xero 5xx, killed process/fpm timeout, concurrency, deploy lag) mints duplicates (non-unique objects) or orphans (invoices). Census shows this leaking since 2025-07. Mutex (added 07-02) covers concurrency only. Prevention ticket to follow.

Side findings: test-box `xero_posting_logs` is stale (ends ~06-25) — live-log forensics must run on live; `/xero/quota` throws on a stale SDK enum (cosmetic, getOrganisations only).
