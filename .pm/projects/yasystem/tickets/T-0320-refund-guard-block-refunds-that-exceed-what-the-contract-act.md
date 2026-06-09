---
id: T-0320
title: "Refund guard: block refunds that exceed what the contract actually holds (shadow mode first)"
type: feature
state: triaged
created: 2026-06-09T19:14:46Z
updated: 2026-06-09T19:14:46Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p0
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - A pre-flight check computes the contract's net refundable cash position (money received minus money genuinely owed, including credit notes) before any refund rows are written and before any Stripe API call
  - With the shadow-mode flag on, over-cap refunds are NOT blocked but a log line records contract number, planned refund total, computed cap, and breakdown
  - With enforcement on, over-cap refunds return a clear error to the user stating the planned total and the cap, and no money moves and no rows are written
  - A test fixture reproducing C090586's state (payment over-applied to the invoice, a later fully-unallocated payment, unused credit notes) is blocked by the guard
  - "Happy paths verified in Stripe test mode: pure credit-note refund, genuine overpayment refund, BACS-only contract, contract with deposit held"
  - Existing refund behaviour is unchanged when the planned total is within the cap
out_of_scope:
  - Changing getUnusedPayment() semantics (separate ticket)
  - The deposit refund flow (Process Deposit Refunds button) — separate code path
  - Restructuring the Stripe-inside-transaction design (separate backlog ticket)
code_anchors:
  - path: backend/controllers/ContractsController.php
    symbol: actionProcessContractRefunds
    lines: 1790-1850
  - path: common/services/PaymentAllocator.php
    symbol: allocate
  - path: common/services/RefundPlanner.php
    symbol: plan
  - path: common/services/RefundExecutor.php
    symbol: execute
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
version: 1
---

## Problem

On contract C090586 a member of the accounts team processed what the screen said was a £138 refund, but the system actually sent the customer £1,399.85 — including a full £1,255.85 Stripe refund they were not owed. The customer themselves flagged it. There is currently no final safety check: each "unused" payment and credit note is refunded independently, with nothing asking "does this contract actually hold this much of the customer's money overall?"

## Context

Investigated 2026-06-09. The wrong refund was triggered by corrupted allocation data (see related tickets), but the missing safety net is what let real money leave. The refund pipeline (allocator → planner → executor) is used by exactly one endpoint, so this change is well contained.

Two existing design constraints the guard MUST respect:

1. Stripe calls happen inside the DB transaction — if anything throws after a Stripe refund succeeds, the DB rolls back but the money is gone with no record. The guard must therefore run as a pre-flight check BEFORE the first Stripe call, never mid-loop.
2. Roll out in shadow mode first: a config flag runs the guard log-only for a week or two so the cap formula can be validated against real refund traffic before it blocks anyone.

## Rollout

Phase 1: shadow (log would-be blocks). Phase 2: review log, enforce.