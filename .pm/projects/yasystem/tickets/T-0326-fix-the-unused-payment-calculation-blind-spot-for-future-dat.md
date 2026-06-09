---
id: T-0326
title: Fix the unused-payment calculation blind spot for future-dated allocations
type: bug
state: triaged
created: 2026-06-09T19:25:36Z
updated: 2026-06-09T19:55:09Z
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
  - Survey query results (count of future-dated payments and invoice-payment rows as of run date) recorded on this ticket BEFORE any code change
  - With no explicit as-of date, the unused-payment calculation includes future-dated allocations — a payment dated tomorrow whose allocation settled an invoice today reports zero unused
  - Every call site that passes an explicit as-of date (both Xero controllers, perspective-date reports) produces byte-identical results before and after — verified against a list of all call sites attached to this ticket
  - "Regression tests cover both modes: no-date (current state, future rows included) and explicit-date (historical, future rows excluded)"
  - "Re-running the C090586 scenario fixture: the phantom second allocation can no longer occur even with a post-dated payment"
out_of_scope:
  - Changing the back-dated timestamps written on allocation rows (load-bearing for Xero period reporting)
  - The Xero-specific variant of the calculation
  - Repairing existing corrupted data (separate ticket)
code_anchors:
  - path: common/models/Payments.php
    symbol: getUnusedPayment
    lines: 148-218
  - path: common/models/Invoices.php
    symbol: getBalances
    lines: 270-318
  - path: common/models/YaContracts.php
    symbol: getUnusedPayment call sites
    lines: 3742,4102,9866
  - path: backend/controllers/XeroNewController.php
    symbol: getUnusedPayment call sites
  - path: backend/controllers/XeroOldController.php
    symbol: getUnusedPayment call sites
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
  - high-blast-radius
attention: null
version: 3
backlog_status: confirmed_for_release
estimated_effort: M
source: discovered
---

## Problem

The function that works out how much of a payment is still unused ignores allocation records dated in the future, while the function that works out an invoice's outstanding balance does not. A payment dated tomorrow therefore looks "fully unused" today even though its allocation already settled the invoice — the same pound can be counted twice. This asymmetry is the root cause of the C090586 phantom allocation.

## Build order

LAST in the sprint — highest blast radius, and the earlier tickets (guard, capped allocation, post-date validation) already make the failure mode unreachable, so this can take its time. Depends on nothing technically, but do it after T-0320/T-0325 so their protections are in place if this regresses something.

## Proposed change

When the calculation is called with no explicit "as of" date (i.e. asking for the CURRENT state), include future-dated allocation rows instead of cutting off at today 23:59:59. All callers that pass an explicit date (Xero period reporting) keep their current behaviour untouched.

## Why this is the highest-risk change in the sprint

~26 call sites across 11 files, including the contract balance calculations (which drive unpaid lists and chasing) and BOTH Xero sync controllers. The timestamp back-dating on allocation rows appears deliberately load-bearing for Xero period reporting — do NOT change the back-dating or the explicit-date behaviour as part of this ticket.

## Testing plan

**Step 0 — survey before any code (record results here):**
Count currently-future-dated rows: payments and invoice_payments with created > now. Expected near zero on any given day. If it is not near zero, STOP and reassess — the change would shift live balances the day it deploys.

**Step 1 — call-site inventory (record here):**
List every call site with whether it passes an explicit date. The known files: the payments model itself, contract balance methods (3 sites), both Xero controllers (5 each), the statement helper, the customer-area view, the manual-refund view/controller, the accounts controller, the payment allocator service. For each: which mode it uses, and therefore whether its behaviour can change.

**Step 2 — golden-master diff (the core test, made possible by the nightly live copy):**
On the test box, BEFORE the change, run a script that computes and stores for every contract with activity in the last 90 days: contract balance, per-payment unused amounts, and per-invoice balances. Apply the change. Re-run and diff. Expected: zero differences except contracts that currently have future-dated rows (per the step-0 survey — likely none). Any other diff is a regression; investigate before proceeding. Keep the script — re-run it after every later change in this area.

**Step 3 — unit tests for both modes:**
- No-date mode: payment dated tomorrow with an allocation row dated tomorrow reports zero unused TODAY (the C090586 condition — this is the test that would have prevented the incident).
- Explicit-date mode: the same fixture viewed with yesterday's perspective date still excludes the future rows (Xero behaviour preserved).
- Refund-side: per-payment refund records follow the same date logic — cover both modes there too.

**Step 4 — Xero regression:**
On the test box, run the Xero export/report paths (against the TEST Xero IDs already configured, never the live tenant) for a completed past month. Output must be identical before and after the change — diff the generated figures, not just spot-check.

**Step 5 — end-to-end re-test of the incident:**
Recreate the full C090586 sequence on the test box WITH this fix and WITHOUT T-0323's validation (temporarily allow a post-dated payment): enter a partial payment dated tomorrow, view the statement page. Expected: no phantom second allocation is created. That closes the loop on the root cause.