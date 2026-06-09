---
id: T-0328
title: Harden refund execution against partial failures (Stripe calls inside the DB transaction)
type: chore
state: triaged
created: 2026-06-09T19:26:15Z
updated: 2026-06-09T19:55:52Z
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
version: 3
backlog_status: in_review
estimated_effort: M
source: discovered
---

## Problem

Two latent risks in the refund executor, found during the C090586 investigation. Neither has fired yet, but both can lose track of real money:

1. Stripe refund calls happen inside the all-or-nothing database transaction. If anything fails after a Stripe refund succeeds (e.g. the second refund in a sequence throws), the database rolls back but the Stripe refund is irreversible — money leaves with no record of it anywhere in the system.
2. The Stripe idempotency keys are derived from auto-increment row IDs that can be rolled back and later reused. If a key is ever reused, Stripe silently returns the ORIGINAL request's result instead of creating the new refund — a refund that looks successful but never happened, or for the wrong amount.

## Build order

Backlog — deliberately NOT in the hardening sprint. Needs its own design pass (options below) and is lower urgency once T-0320's pre-flight guard removes the main mid-loop throw paths. Sequencing after the sprint also avoids three sets of hands in the same file at once. Requires T-0331 (dry-run) for testing.

## Design options to evaluate (record the choice as an ADR)

- **Commit-per-refund:** each refund's DB rows commit before the next Stripe call; a mid-sequence failure leaves earlier refunds recorded.
- **Post-commit execution:** plan and write intent rows in the transaction, execute Stripe calls after commit, mark each row with the Stripe result; a reconciler flags intent rows that never got a result.
- **Outbox pattern:** full async — intent rows consumed by a worker. Most robust, most moving parts.
- Idempotency keys in all options: switch to a globally unique value (e.g. UUID stored on the refund row at creation), never a bare auto-increment ID.

## Testing plan (applies to whichever design wins)

**On the test box (sandbox + dry-run):**

1. **Fault injection — the core test:** instrument the executor (test-only hook) to throw immediately AFTER the first dry-run Stripe request of a multi-refund plan. Expected: the first refund's DB rows survive and match the dry-run log; the failure is reported; nothing about the second refund exists. Repeat with the failure before the first call (nothing recorded anywhere) and between record-write and Stripe call (per the chosen design's guarantee — document it).
2. **Reconciliation check:** whatever the design, there must be a query (or screen) that lists refund records with no Stripe result and would-be Stripe results with no record. Run it after each fault-injection case — it must flag exactly the expected discrepancies and nothing else.
3. **Idempotency key uniqueness:** unit test that two refund attempts created after a rollback (simulated by deleting the first attempt's rows) produce different keys. Verify the key is persisted on the refund row so a retry of the SAME intended refund reuses the SAME key (that is what idempotency keys are for — retry safety, not uniqueness alone).
4. **Replay regression:** re-run the T-0320 replay set through the reworked executor in dry-run; the logged requests must be unchanged from before the rework — this change must alter failure behaviour only, never the happy path.
5. **Crash-mid-sequence drill:** kill the PHP process between two refunds of a plan (manual or scripted); on restart, the reconciliation query must surface the partial state and the documented recovery procedure must resolve it. Write that procedure into this ticket as part of the work.