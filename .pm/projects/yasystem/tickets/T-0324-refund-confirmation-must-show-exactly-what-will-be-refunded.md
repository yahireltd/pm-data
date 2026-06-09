---
id: T-0324
title: Refund confirmation must show exactly what will be refunded
type: bug
state: triaged
created: 2026-06-09T19:24:53Z
updated: 2026-06-09T19:27:47Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - "The refund modal lists each individual refund that will be executed: source payment reference, method (Stripe/BACS), and amount, plus the grand total"
  - The preview figures are derived from the same planning logic the executor runs, not a separate calculation that can drift
  - Generating the preview performs zero database writes and zero Stripe refund creations (read-only Stripe lookups are acceptable)
  - For a fixture reproducing C090586's pre-refund state, the modal shows the full £1,399.85 plan (or, once the T-0320 guard is enforced, the blocked state) — never a different number to what would execute
  - For a normal contract with a simple credit-note refund the preview matches the executed refund to the penny
out_of_scope:
  - Changing what gets refunded (guard and calculation fixes are separate tickets)
  - Redesigning the modal beyond the figures shown
code_anchors:
  - path: backend/views/contracts/manual-refund.php
    symbol: refundModal totals
    lines: 255-330
  - path: backend/views/contracts/manual-refund.php
    symbol: "#refundModal markup"
    lines: 580-689
  - path: common/services/RefundPlanner.php
    symbol: plan
  - path: common/services/PaymentAllocator.php
    symbol: allocate
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

In the C090586 incident the refund modal told the user "Refund £138" but clicking the button refunded £1,399.85. The number on screen and the action behind the button are calculated by two different pieces of code that disagree: the on-screen total nets positive and negative "unused" amounts against each other, while the execution path silently drops the negatives and refunds every positive in full. The user approved a number that had nothing to do with what happened.

## Context

The fix is to make the modal display the actual refund plan — the same plan the executor will run: one line per refund showing source payment, method (Stripe or BACS), and amount, plus the total.

CRITICAL TRAP for whoever builds this: the allocation step of the refund pipeline writes to the database — you cannot "dry-run" the existing pipeline as-is to build the preview. The preview must be computed side-effect-free (the planner itself is safe; its Stripe calls are read-only).

With T-0320's guard in place this becomes a transparency fix rather than the only line of defence, which is why it is sequenced after the guard.