---
id: T-0662
title: Batch picker (combobox) on quality failure points + quality checks, sourced from the product's recorded order batches
type: feature
state: in_progress
created: 2026-07-24T09:52:46Z
updated: 2026-07-24T09:53:03Z
project: stock-management-development
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: chat 24 Jul
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "On the product quality page, the failure-point Batch reference field and the quality-check add + edit forms use one shared inline searchable combobox that suggests the product's recorded batch numbers (from its supplier order items), showing order # / supplier / date context, and still allows a free-typed value."
  - "The combobox is client-side: it filters a server-rendered list of that product's batches (no new AJAX endpoint, so no RBAC route to grant); matches by batch #, order number, supplier or date; selecting stores the batch number (not the rich label)."
  - "Failure points: the existing free-text batchRef input is upgraded in place — no schema change; still only saved when scope = batch; card display unchanged."
  - "Quality checks: gain an optional batch (new nullable column batch_number on stock_quality_checks), captured on add AND edit, saved, shown on the check card; legacy checks show blank."
  - Products with no recorded batches behave exactly like the plain optional text field they are today (empty suggestion list).
out_of_scope:
  - Per-batch stock quantity tracking / FIFO (separate spike).
  - A dedicated per-supplier short-code column (batch codes come from the recorded batch numbers).
  - Server-side enforcement of the batch format (stays free text).
code_anchors:
  - path: sql/stock_quality_checks_batch_number.sql
    note: "NEW manual DDL: ALTER TABLE stock_quality_checks ADD batch_number VARCHAR(50) NULL (run once; legacy rows NULL)"
  - path: ya-hire/common/models/StockQualityCheck.php
    note: add batch_number to rules (string max 50) + docblock + label; beforeSave '' -> null
  - path: ya-hire/backend/controllers/StockController.php
    note: NEW productBatchOptions($productId) helper (item_id->recorded batches + order/supplier/date labels, deduped); actionViewProductInfo pass batchOptions to view; qualitySectionModal (add) + QC edit-form builder (~L21788) add batch input; actionSaveQualityCheck (~L20919/L21055) + actionUpdateQualityCheck read/save batch_number; renderQualityCheckCard (~L16048) show batch. FP save (actionSaveFailurePoint) unchanged — batchRef already handled.
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: "output window.productBatchOptions JS var; shared attachBatchAutocomplete() (jQuery UI autocomplete over the array, appendTo modal, store value on select); FP init in fpToggleBatchRef (#fpModalBatchRef, #failurePointModalID); QC delegated-focus lazy init (.qc-batch-input, #editSectionModalID); append batch in saveQualityCheck + saveQualityCheckEdit"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260724-0953
    model: claude-opus-4-8
    started: 2026-07-24T09:53:03Z
    status: in_progress
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-24T09:53:03Z
labels:
  - suppliers
  - orders
  - stock
  - quality-management
  - ui
attention: null
version: 3
---

## Problem
T-0651 added batch numbers on supplier order line items. To make quality issues traceable to a batch, quality features need to reference those batches. Failure points already have a free-text `batchRef` (easy to mistype); quality checks have no batch at all.

## Solution
One shared inline **searchable combobox** on the product quality page, sourced from that product's recorded order-item batches (they share the product id: stockID = YaProducts.id = StockSupplierOrderItems.item_id). Shows order/supplier/date context so users pick by what they remember; free text still allowed. Client-side (filters a server-rendered array) so no new endpoint / RBAC.

- **Failure points:** upgrade the existing batchRef input in place (no schema change).
- **Quality checks:** add an optional batch (new nullable column), captured on add + edit, shown on the card.

## Deferred (out of scope)
Per-batch stock quantities / FIFO (spike); dedicated supplier code column; format enforcement.

## Source
Chat 24 Jul (Zsolt) — follow-up to T-0651; agreed inline searchable combobox, same for both features.