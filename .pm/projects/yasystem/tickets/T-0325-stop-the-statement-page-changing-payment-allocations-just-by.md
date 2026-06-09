---
id: T-0325
title: Stop the statement page changing payment allocations just by being viewed
type: bug
state: triaged
created: 2026-06-09T19:25:16Z
updated: 2026-06-09T19:54:44Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - Loading the manual-refund page performs zero database writes (verified by query logging during a page load on a contract with unallocated payments and credits)
  - Allocation is available as an explicit user action that shows what it is about to apply before doing it
  - Any allocation path is capped at the payment's true remaining unapplied amount — it can never apply more than the payment has left
  - The stale stored-balance ordering bug in the allocation loops is fixed (balance column is decremented by the amount actually applied)
  - Accounts team briefed on the workflow change before release; their sign-off recorded on this ticket
  - Invoices with genuinely unallocated payments still get allocated through the new explicit action and no longer appear unpaid afterwards
out_of_scope:
  - The equivalent auto-allocation inside payment recording (runs at entry time with the new payment — correct behaviour)
  - Xero sync logic
code_anchors:
  - path: backend/controllers/ContractsController.php
    symbol: actionManualRefund GET-side auto-allocator
    lines: 563-620
  - path: backend/controllers/PaymentsController.php
    symbol: actionAllocatePayment
    lines: 251-307
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
attention: null
version: 3
backlog_status: confirmed_for_release
estimated_effort: M
source: discovered
---

## Problem

Opening the Process Refunds / statement page silently rewrites financial records: on every page view it re-applies any "unused" payments and credit notes to invoices. In the C090586 incident this page-view side effect is what physically created the phantom £1,261.85 allocation (combined with the future-dated payment blind spot). Viewing a page should never move money around.

## Build order

After T-0320/T-0324 (it shares the same screen as T-0324 — coordinate). Requires an accounts-team conversation before release (below).

## Context

Care needed: this auto-tidy is probably load-bearing for the accounts team's daily workflow — it is how unallocated payments end up applied to invoices without anyone pressing anything. Removing it outright could surface as invoices wrongly showing unpaid (chasing lists, Xero export). Errors in that direction are safe (no money leaves) but disruptive.

Preferred shape: move the allocation behind an explicit user action (a visible "Allocate" button doing a POST that shows what it is about to apply), and cap any allocation at the payment's true remaining unapplied amount regardless of date filters — so even if T-0326's calculation fix were missing or regressed, an over-application is impossible at this layer too (defence in depth).

Also fix in passing: the ordering bug where the stored invoice balance column is decremented AFTER the remaining-amount variable has been zeroed, so the column is reduced by zero and goes stale on every partial allocation. The same pattern appears in three places (page-view allocator, payment recording, the allocate-payment endpoint) — fix all three, they are anchored.

## Testing plan

**On the test box (sandbox + dry-run):**

1. **No-write page view:** enable the DB query log; load the manual-refund page for (a) a contract with an unallocated payment, (b) one with an unused credit note, (c) one fully settled. Expected: zero INSERT/UPDATE from the allocator region in all three. (The page header currently also recalculates and saves contract totals on view — decide explicitly whether that stays, and record the decision here; it is a separate side effect.)
2. **Explicit allocate action:** on contract (a), use the new Allocate button. Expected: a preview of what will be applied, then on confirm exactly those allocation rows are written, capped at the payment's remaining amount. Verify row-level: amounts, invoice IDs, payment IDs.
3. **Over-application impossible:** construct the C090586 trigger deliberately — a payment whose allocation rows already consume it in full, with the invoice still showing a balance (the nightly copy contains C090586 itself in exactly this state). Run the explicit allocate. Expected: it applies nothing from the exhausted payment and says so.
4. **Stale-balance fix:** make a partial payment against a large invoice on the test box; verify the invoice's stored balance column equals the computed balance afterwards (today it does not — it is reduced by zero). Then verify the unpaid-invoices list and any screen reading the stored column show the same figure as the statement page.
5. **Workflow regression week:** after deploy, watch the unpaid-invoices list for unexpected growth (invoices that would previously have been silently tidied). Pull the same list on the test box before/after the change for the same nightly data and diff — that gives you the expected delta in advance, not a surprise in production.
6. **Accounts sign-off:** walk the accounts team through the new explicit step BEFORE release; record their sign-off and any workflow notes here.
7. **Cross-impact:** the same auto-allocation pattern exists at payment-recording time (correct — it allocates the NEW payment) and in the allocate-payment endpoint. Confirm payment entry still allocates normally end-to-end, and that the endpoint now shares the capped logic.