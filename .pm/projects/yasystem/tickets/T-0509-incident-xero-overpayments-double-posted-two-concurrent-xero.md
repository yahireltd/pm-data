---
id: T-0509
title: "INCIDENT: Xero overpayments double-posted — two concurrent xero/run-post executions (2026-07-02 09:27–09:38)"
type: incident
state: triaged
created: 2026-07-02T14:48:35Z
updated: 2026-07-02T14:48:35Z
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
version: 1
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