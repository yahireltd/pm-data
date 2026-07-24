---
id: T-0663
title: 'Failure points: rename "Specific batch" scope to "Part of stock" with an optional batch (covers unknown-batch partial issues)'
type: feature
state: review
created: 2026-07-24T10:53:17Z
updated: 2026-07-24T10:54:45Z
project: stock-management-development
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt
  channel: chat 24 Jul
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - The failure-point Scope dropdown's second option reads 'Part of stock' instead of 'Specific batch'; the stored value stays 'batch' (no data migration, isStale review behaviour unchanged).
  - "Under 'Part of stock' the Qty affected field (already always shown) and the Batch # picker both appear; the batch is explicitly OPTIONAL — leaving it blank records a partial-stock issue with no/unknown batch."
  - "The failure-point card shows 'Part of stock' (with '· Batch: X' appended only when a batch was given), replacing the old 'Batch'/'Batch: X' label."
  - The T-0662 batch combobox still works on the (now optional) batch field; free-typed values still allowed.
  - "'Whole stock' scope is unchanged."
out_of_scope:
  - Renaming the stored scope enum values or the SCOPE_BATCH constant.
  - Hiding/gating the Qty affected field by scope (it stays always-visible as today).
code_anchors:
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: "FP modal: scope option label 'Specific batch' -> 'Part of stock' (value stays 'batch', ~L1066); batch field label 'Batch reference' -> 'Batch # (optional)' + placeholder (~L1072-1073)"
  - path: ya-hire/backend/controllers/StockController.php
    note: "renderFailurePointCard (~L16039): scopeLabel 'Batch'/'Batch: X' -> 'Part of stock' / 'Part of stock · Batch: X'"
  - path: ya-hire/common/models/StockQualityFailurePoint.php
    note: docblock comment for batchRef (optional under part-of-stock); no logic/enum change
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260724-1053
    model: claude-opus-4-8
    started: 2026-07-24T10:53:26Z
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-24T10:53:26Z
    ended: 2026-07-24T10:54:45Z
    summary: |-
      Fixed a gap in how quality failure points are scoped. Before, a failure point was either "Whole stock" or "Specific batch" — but staff hit the in-between case a lot: the problem affects only some of the stock, but there's no known/recorded batch. They were forced to either wrongly call it "Whole stock" or pick "Specific batch" and leave the reference empty.

      The second option is now "Part of stock": you say how many units are affected (as before) and the batch number is optional — pick a recorded batch if you know it, or leave it blank when you don't. So all three real situations are covered by one clear option. The failure-point card now reads "Part of stock" (adding "· Batch: X" only when a batch was given).

      Why it matters: staff can now record partial-stock quality issues honestly instead of shoehorning them into the wrong category, which keeps the quality picture accurate. Nothing changed in the data or existing records — the internal value is unchanged, so old entries and the review-reminder behaviour are untouched; only the wording and the optional-ness changed.
    test_plan: |-
      No schema/SQL change; label + optional-field change only. On a product's Quality section:

      1. Add a Failure Point → the Scope dropdown now reads "Whole stock" / "Part of stock".
      2. Choose "Part of stock" → the "Qty affected" field (already shown) plus a "Batch # (optional)" field appear. The batch field is the T-0662 picker — focus it to pick a recorded batch, or type your own, or LEAVE IT BLANK. Save with a blank batch → it saves fine (records a partial-stock issue, no batch).
      3. Save with a batch chosen → the card shows "Part of stock · Batch: <value>". Save with blank → the card shows just "Part of stock".
      4. "Whole stock" scope is unchanged (card shows "Whole stock").
      5. Edit an EXISTING batch-scoped failure point (created before this change) → it opens as "Part of stock" with its batch pre-filled; re-save works; the "Needs review" (staleness) pill/tooltip still behaves as before (now worded "Part-of-stock point…").
      6. Regression: the batch combobox still filters the product's recorded batches; free text still allowed; adding/editing/deleting failure points otherwise unchanged.

      Doc: docs/features/stock-quality-failure-points.md updated.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Updated docs/features/stock-quality-failure-points.md scope/batchRef rows to reflect the 'Part of stock' label + optional batch (T-0663), cross-refs T-0662.
labels:
  - stock
  - quality-management
  - ui
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-24T10:54:45Z
version: 4
---

## Problem
Failure-point Scope is a hard binary: Whole stock vs Specific batch. Real cases fall in between — "it's not all the stock, but I don't have/know a batch." Today you'd mislabel it Whole stock or pick Specific batch and leave the reference blank.

## Solution (Option 1, agreed)
Relabel the second scope to **Part of stock** and make the batch **optional** under it (the Qty affected field is already always shown, and the batch reference is already saved-only-if-non-empty). One option now covers: specific batch (fill batch), some units with unknown batch (leave blank), some units with a known batch. Stored value stays `batch` — no migration, and `isStale()` review nagging still applies (appropriate for partial-stock issues).

## Source
Chat 24 Jul (Zsolt) — chose Option 1 while reviewing the T-0662 batch picker.

## Conversation

**2026-07-24 10:54 claude-code:** Run run-20260724-1053 completed — Fixed a gap in how quality failure points are scoped. Before, a failure point was either "Whole stock" or "Specific batch" — but staff hit the in-between case a lot: the problem affects only some of the stock, but there's no known/recorded batch. They were forced to either wrongly call it "Whole stock" or pick "Specific batch" and leave the reference empty.

The second option is now "Part of stock": you say how many units are affected (as before) and the batch number is optional — pick a recorded batch if you know it, or leave it blank when you don't. So all three real situations are covered by one clear option. The failure-point card now reads "Part of stock" (adding "· Batch: X" only when a batch was given).

Why it matters: staff can now record partial-stock quality issues honestly instead of shoehorning them into the wrong category, which keeps the quality picture accurate. Nothing changed in the data or existing records — the internal value is unchanged, so old entries and the review-reminder behaviour are untouched; only the wording and the optional-ness changed.
