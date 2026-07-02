---
id: T-0509
title: "INCIDENT: Xero overpayments double-posted — two concurrent xero/run-post executions (2026-07-02 09:27–09:38)"
type: incident
state: triaged
created: 2026-07-02T14:48:35Z
updated: 2026-07-02T15:02:04Z
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
version: 2
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
