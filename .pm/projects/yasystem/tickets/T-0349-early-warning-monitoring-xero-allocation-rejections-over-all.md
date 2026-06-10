---
id: T-0349
title: "Early-warning monitoring: Xero allocation rejections + over-allocation and future-date sweeps"
type: feature
state: triaged
created: 2026-06-10T13:59:41Z
updated: 2026-06-10T13:59:41Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
  channel: claude-code
assignee: null
acceptance_criteria:
  - An alert (email or equivalent, visible to someone who will act on it) fires when a new xero_posting_logs row has status='error' and a message matching the 'greater than the amount remaining' class
  - A scheduled job (or documented daily/weekly routine) runs the over-allocation sweep (payments where SUM(invoice_payments.amountApplied) > payments.amount + 0.01) and the future-dated-rows survey (payments/invoice_payments/deposits with created > NOW()), and surfaces any new hits
  - "Baseline results at go-live are attached to this ticket (expected: only historic payment 2493 in the over-allocation sweep, zero future-dated rows after the T-0327 repairs)"
  - "A short runbook on this ticket says what to do on a hit: do not open the manual-refund screen for the affected customer until the dates/allocations are corrected; link to the repair recipe on T-0327"
out_of_scope:
  - Fixing the underlying calculation (T-0326) or allocation writers (T-0325) — this is the detection net, not the fix
  - General Xero sync error handling beyond this error class
code_anchors:
  - path: sql/incident-c090586-proof-queries.sql
    symbol: Q10 over-allocation sweep, Q11 future-date survey, Q12 hidden-allocation landmines, Q14 Xero rejection sweep
  - path: console/migrations/m230525_000000_create_xero_posting_logs_table.php
    symbol: xero_posting_logs
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - refunds
  - payments
  - monitoring
  - incident-c090586
attention: null
version: 1
---

## Problem

During the C090586 incident, Xero's own ledger rejected the phantom allocation on **20 May 2026** — five days before £1,399.85 left for the customer on 25 May. The rejection (`xero_posting_logs` row 70622: *"Overpayment cannot be allocated as the allocation amount is greater than the amount remaining on the Overpayment"*) sat unread; it re-fired on 5 June (row 71029) and is still retrying. Xero independently validates every allocation we post, which makes this error class a free, third-party corruption detector — we just don't look at it.

## Context

Three cheap detection signals exist today, all proven during the 2026-06-10 investigation (queries in repo file `sql/incident-c090586-proof-queries.sql`):

1. **Xero rejection sweep (Q14)** — catches over-allocations on any contract that posts to Xero, days before a refund can go wrong.
2. **Over-allocation sweep (Q10)** — `SUM(invoice_payments.amountApplied) > payments.amount + 0.01` grouped by payment; catches corruption whether or not Xero posting has run. Current baseline: only payment 47215 (C090586, being repaired under T-0327) and historic payment 2493 (2021).
3. **Future-dated rows survey (Q11/Q12)** — payments or allocations dated after today are landmines for the `getUnusedPayment` blind spot (T-0326). Current hits: payments 44320/44321/44322 (£3,535.93, year-typo, repair under T-0327).

Until T-0325/T-0326 land, these sweeps are the only systematic way to spot the next C090586 before a refund click. After they land, the sweeps verify the fixes hold (T-0332 already requires the detection query "must not grow" at phase gates — this ticket makes that a standing check rather than a one-off).

## Implementation sketch

Lightweight is fine: a console command running the three queries on a daily cron, mailing/flagging only non-baseline hits. The Xero rejection check can alternatively hook wherever xero_posting_logs errors are already handled, if anywhere.