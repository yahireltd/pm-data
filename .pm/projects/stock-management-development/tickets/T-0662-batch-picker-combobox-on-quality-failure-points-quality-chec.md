---
id: T-0662
title: Batch picker (combobox) on quality failure points + quality checks, sourced from the product's recorded order batches
type: feature
state: review
created: 2026-07-24T09:52:46Z
updated: 2026-07-24T10:29:13Z
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
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-24T09:53:03Z
    ended: 2026-07-24T10:29:13Z
    summary: |-
      Made it easy to tie a quality issue to the batch it came from. On a product's quality page, both the failure-point form and the quality-check form now have a Batch # box that suggests that product's actual recorded batches as you type — showing the order number, supplier and date so you recognise the right one — while still letting you type a value that isn't in the list.

      Previously failure points had a plain "batch reference" text box (easy to mistype) and quality checks had no batch at all. Now they share one consistent picker, sourced from the batches recorded against that product's supplier orders, so a quality problem can be traced back to a specific delivery.

      Why it matters: batch numbers are only useful if quality records actually reference them consistently. Letting staff pick the real batch (by the order/supplier/date they remember) instead of retyping a code is what makes that traceability real. It's built to be safe: if the underlying batch columns haven't been added to the database yet, the pages carry on working and the picker simply shows no suggestions. No prices, quantities, stock levels or existing records are changed.
    test_plan: |-
      Two setup steps, then the checks. All on a product's quality page (view-product-info). Best tested on a product that HAS supplier orders with batch numbers (from T-0651).

      SETUP (run once each; until then the picker shows no suggestions but nothing breaks):
      1. Run sql/stock_supplier_order_items_batch_number.sql (T-0651 — the order-item batch column) if not already done.
      2. Run sql/stock_quality_checks_batch_number.sql (this ticket — quality-check batch column).

      FAILURE POINTS:
      1. Open a product → Quality section → add a Failure Point → set Scope = "Batch" → the Batch reference field appears. Focus it → a dropdown lists that product's recorded batches as "FLEX-260724-01 · PO1042 · Flexfurn · 24 Jul". Type to filter by batch #, PO, supplier or date. Pick one → the field holds the batch number (e.g. FLEX-260724-01). Save → the failure-point card shows "Batch: FLEX-260724-01".
      2. Type a value that isn't in the list → it's accepted (free text still works). Edit an existing batch failure point → the picker works there too.
      3. Product with no recorded batches → the field behaves like a plain optional text box.

      QUALITY CHECKS:
      4. Add a Quality Check → there's a new "Batch # (optional)" box below the note. Focus → same picker/suggestions. Pick or type a batch → Save → the check card shows a blue "Batch: …" badge.
      5. Edit that check → the batch box is pre-filled; change/clear it → Save → badge updates/clears. Leaving it blank saves fine (optional).
      6. Legacy checks (pre-SQL) show no batch and are unaffected.

      REGRESSION / SAFETY:
      7. With NEITHER sql run: open the product page, add a failure point, add a quality check — all still work; the batch pickers just show no suggestions and save blank. (Confirms the hasAttribute guards.)
      8. The batch picker is client-side only — confirm no new permission errors (no new endpoint was added).

      Docs: docs/features/stock-supplier-order-batch-numbers.md ("Batch picker on quality (T-0662)").
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Added 'Batch picker on quality (T-0662)' section to docs/features/stock-supplier-order-batch-numbers.md (shared combobox, sourcing, graceful-degradation guards).
labels:
  - suppliers
  - orders
  - stock
  - quality-management
  - ui
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-24T10:29:13Z
version: 4
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

## Conversation

**2026-07-24 10:29 claude-code:** Run run-20260724-0953 completed — Made it easy to tie a quality issue to the batch it came from. On a product's quality page, both the failure-point form and the quality-check form now have a Batch # box that suggests that product's actual recorded batches as you type — showing the order number, supplier and date so you recognise the right one — while still letting you type a value that isn't in the list.

Previously failure points had a plain "batch reference" text box (easy to mistype) and quality checks had no batch at all. Now they share one consistent picker, sourced from the batches recorded against that product's supplier orders, so a quality problem can be traced back to a specific delivery.

Why it matters: batch numbers are only useful if quality records actually reference them consistently. Letting staff pick the real batch (by the order/supplier/date they remember) instead of retyping a code is what makes that traceability real. It's built to be safe: if the underlying batch columns haven't been added to the database yet, the pages carry on working and the picker simply shows no suggestions. No prices, quantities, stock levels or existing records are changed.
