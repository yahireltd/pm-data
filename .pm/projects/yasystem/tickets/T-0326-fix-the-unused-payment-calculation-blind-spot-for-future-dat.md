---
id: T-0326
title: Fix the unused-payment calculation blind spot for future-dated allocations
type: bug
state: triaged
created: 2026-06-09T19:25:36Z
updated: 2026-06-09T19:27:49Z
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
version: 2
backlog_status: confirmed_for_release
estimated_effort: M
source: discovered
---

## Problem

The function that works out how much of a payment is still unused ignores allocation records dated in the future, while the function that works out an invoice's outstanding balance does not. A payment dated tomorrow therefore looks "fully unused" today even though its allocation already settled the invoice — the same pound can be counted twice. This asymmetry is the root cause of the C090586 phantom allocation.

## Proposed change

When the calculation is called with no explicit "as of" date (i.e. asking for the CURRENT state), include future-dated allocation rows instead of cutting off at today 23:59:59. All callers that pass an explicit date (Xero period reporting) keep their current behaviour untouched.

## Context — why this is the highest-risk change in the sprint

This function has ~26 call sites across 11 files, including the contract balance calculations (which drive unpaid lists and chasing) and BOTH Xero sync controllers. The timestamp back-dating on allocation rows appears deliberately load-bearing for Xero period reporting — do NOT change the back-dating or the explicit-date behaviour as part of this ticket.

Required first step: run a survey query counting currently-future-dated payment and allocation rows, and record the result on this ticket. Expected near zero on any given day — if it is not, stop and reassess impact before changing anything.