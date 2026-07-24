---
id: T-0661
title: "Supplier orders: auto-suggest batch number (scheme B: SUP-YYMMDD-NN) in the Add Order Item forms"
type: feature
state: review
created: 2026-07-24T08:34:28Z
updated: 2026-07-24T08:39:22Z
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
  - "The Add Order Item forms (header modal + in-Edit-Order form) pre-fill the Batch # field with a suggested value, editable, only when the field is empty (never clobbers user input)."
  - "Format is scheme B: SUP-YYMMDD-NN. SUP = up to 4 leading alphanumerics of the supplier name, uppercase. YYMMDD = the order's arrival date, falling back to order date, then today. NN = 2-digit sequence."
  - The sequence increments past any existing batch on that order sharing the same SUP-YYMMDD- prefix (so a second same-day item suggests -02, not a duplicate -01).
  - The suggestion is a default only — the user can overwrite or clear it; nothing is auto-saved. Blank remains valid.
  - No schema or endpoint changes — client-side suggestion layered on the existing add flow (T-0651).
out_of_scope:
  - A dedicated per-supplier short-code column (code is derived from the name for now).
  - Server-side enforcement/validation of the format (batch stays free-text).
code_anchors:
  - path: ya-hire/backend/views/stock/supplier-view.php
    note: "build a batchSuggestState JS map (supplierName + per-order date & existing batches from $supplier->orders); add batchSupplierCode/batchDatePart/nextBatchSeq/suggestBatchForOrder helpers; prefill on #order-item-order-select change (header modal) and on Edit Order open (modal-order-item-batch); refresh state + re-suggest after a successful add"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260724-0834
    model: claude-opus-4-8
    started: 2026-07-24T08:34:39Z
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-24T08:34:39Z
    ended: 2026-07-24T08:39:22Z
    summary: |-
      Made the Batch # field on supplier order items suggest a value automatically, so batch numbers follow one consistent house style instead of everyone inventing their own.

      When you add an item to an order, the Batch # box now starts pre-filled with a suggestion in the agreed format — a short supplier code, the delivery date, and a running number (e.g. FLEX-260724-01 for Flexfurn, 24 Jul 2026, first batch that day). It's only a starting point: you can change or clear it, and a second item added to the same order that day is suggested "-02" so you don't get duplicates. Nothing is forced — the field stays free text and blank is still allowed.

      Why it matters: batch numbers are only useful if they're consistent enough to search and trace. Auto-suggesting the pattern means staff follow it by default rather than by memory, without any extra typing. This is a small convenience layer on top of the batch feature (T-0651) — no database or saving changes.
    test_plan: |-
      Depends on T-0651 being live (the batch column + SQL). All changes are client-side on the Supplier View page.

      1. Header Add Order Item: open a supplier → "Add Order Item" → pick an order from the dropdown → the Batch # field auto-fills as SUP-YYMMDD-NN (supplier code + that order's arrival date + 01). Change the order in the dropdown → suggestion updates. Type your own value → it is NOT overwritten when you change the order.
      2. Sequence: on an order that already has a batch like FLEX-260724-01, adding another item the same day suggests FLEX-260724-02 (not a duplicate 01).
      3. Edit Order modal: open "Edit Order" on an order → the "Add Item to Order" Batch # box is pre-filled with the next suggestion. Add an item → the box re-fills with the next number bumped.
      4. Editable/optional: clear the suggested batch and save → item saves with blank batch (still valid). Overwrite with a custom value → your value is what saves.
      5. Date fallback: an order with no arrival date uses the order date; with neither, uses today.
      6. Supplier code: e.g. "Flexfurn" → FLEX; a short name like "3M" → 3M. Uppercase, no spaces.
      7. Regression: adding an item still works normally (item saves, panel refreshes); the batch you see suggested is what lands in the Batch column unless you edit it.

      Doc: docs/features/stock-supplier-order-batch-numbers.md (Auto-suggested batch section).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Added an 'Auto-suggested batch (scheme B — T-0661)' section to docs/features/stock-supplier-order-batch-numbers.md.
labels:
  - suppliers
  - orders
  - stock
  - ui
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-24T08:39:22Z
version: 4
---

## Problem
T-0651 added a free-text Batch # to order line items. To keep batch numbers consistent, we agreed on **scheme B — `SUP-YYMMDD-NN`** (supplier code + order arrival date + sequence). Free text alone relies on discipline; a pre-filled, editable suggestion makes the pattern the default.

## Solution
Client-side auto-suggest layered on the existing add flow: when adding an order item, pre-fill the Batch # field with `SUP-YYMMDD-NN` built from the supplier name + the order's arrival date + the next sequence (from existing batches on that order sharing the prefix). Editable; only fills when empty.

## Notes
- Supplier code is derived from the name (first 4 alphanumerics, uppercase) — no dedicated code column yet (out of scope).
- Header Add Order Item modal builds the suggestion when an order is picked; Edit Order modal builds it on open and re-suggests after each add.

## Source
Chat 24 Jul (Zsolt) — agreed scheme B and asked for the auto-suggest follow-up to T-0651.

## Conversation

**2026-07-24 08:39 claude-code:** Run run-20260724-0834 completed — Made the Batch # field on supplier order items suggest a value automatically, so batch numbers follow one consistent house style instead of everyone inventing their own.

When you add an item to an order, the Batch # box now starts pre-filled with a suggestion in the agreed format — a short supplier code, the delivery date, and a running number (e.g. FLEX-260724-01 for Flexfurn, 24 Jul 2026, first batch that day). It's only a starting point: you can change or clear it, and a second item added to the same order that day is suggested "-02" so you don't get duplicates. Nothing is forced — the field stays free text and blank is still allowed.

Why it matters: batch numbers are only useful if they're consistent enough to search and trace. Auto-suggesting the pattern means staff follow it by default rather than by memory, without any extra typing. This is a small convenience layer on top of the batch feature (T-0651) — no database or saving changes.
