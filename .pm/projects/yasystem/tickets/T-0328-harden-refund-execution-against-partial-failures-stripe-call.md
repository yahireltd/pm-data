---
id: T-0328
title: Harden refund execution against partial failures (Stripe calls inside the DB transaction)
type: chore
state: triaged
created: 2026-06-09T19:26:15Z
updated: 2026-06-09T19:27:50Z
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
  - A failure between two Stripe refunds in one run leaves the database matching what Stripe actually did (each executed refund has its record committed)
  - Stripe idempotency keys are globally unique and cannot collide after a rollback or counter reset
  - A test simulates a mid-sequence failure and verifies no orphan Stripe refund (money moved, no record) can occur
  - Design decision (commit-per-refund vs post-commit execution vs outbox) recorded as an ADR
out_of_scope:
  - The pre-flight cash-position guard (T-0320)
  - The deposit refund flow (same pattern — assess separately once this lands)
code_anchors:
  - path: common/services/RefundExecutor.php
    symbol: execute — payment refunds Stripe block
    lines: 96-138
  - path: common/services/RefundExecutor.php
    symbol: execute — credit-note chunk loop
    lines: 207-281
  - path: backend/controllers/ContractsController.php
    symbol: actionProcessContractRefunds transaction
    lines: 1810-1849
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - refunds
  - payments
  - tech-debt
attention: null
version: 2
backlog_status: in_review
estimated_effort: M
source: discovered
---

## Problem

Two latent risks in the refund executor, found during the C090586 investigation. Neither has fired yet, but both can lose track of real money:

1. Stripe refund calls happen inside the all-or-nothing database transaction. If anything fails after a Stripe refund succeeds (e.g. the second refund in a sequence throws), the database rolls back but the Stripe refund is irreversible — money leaves with no record of it anywhere in the system.
2. The Stripe idempotency keys are derived from auto-increment row IDs that can be rolled back and later reused. If a key is ever reused, Stripe silently returns the ORIGINAL request's result instead of creating the new refund — a refund that looks successful but never happened, or for the wrong amount.

## Context

Backlog item, deliberately NOT in the hardening sprint: it needs its own design pass (commit-per-refund vs. post-commit execution vs. outbox pattern) and is lower urgency once the pre-flight guard (T-0320) removes the main mid-loop throw paths. Sequencing it after the sprint also avoids three sets of hands in the same file at once.