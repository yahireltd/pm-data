---
id: T-0325
title: Stop the statement page changing payment allocations just by being viewed
type: bug
state: triaged
created: 2026-06-09T19:25:16Z
updated: 2026-06-09T19:27:48Z
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
version: 2
backlog_status: confirmed_for_release
estimated_effort: M
source: discovered
---

## Problem

Opening the Process Refunds / statement page silently rewrites financial records: on every page view it re-applies any "unused" payments and credit notes to invoices. In the C090586 incident this page-view side effect is what physically created the phantom £1,261.85 allocation (combined with the future-dated payment blind spot). Viewing a page should never move money around.

## Context

Care needed: this auto-tidy is probably load-bearing for the accounts team's daily workflow — it is how unallocated payments end up applied to invoices without anyone pressing anything. Removing it outright could surface as invoices wrongly showing unpaid (chasing lists, Xero export). Errors in that direction are safe (no money leaves) but disruptive.

Preferred shape: move the allocation behind an explicit user action (a visible "Allocate" button doing a POST), and cap any allocation at the payment's true remaining amount regardless of date filters. Walk the accounts team through the change before it ships.

Note: there is also a latent ordering bug in these loops — the stored invoice balance column is decremented by a zeroed variable, leaving it stale after partial allocations. Fix in passing.