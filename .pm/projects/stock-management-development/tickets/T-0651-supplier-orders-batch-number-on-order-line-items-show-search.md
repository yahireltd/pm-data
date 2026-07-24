---
id: T-0651
title: "Supplier orders: batch number on order line items (+ show/search)"
type: feature
state: in_progress
created: 2026-07-23T10:58:21Z
updated: 2026-07-24T08:05:06Z
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
  channel: chat 23 Jul
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "Supplier order line items have an optional Batch # field (new nullable column batch_number on stock_supplier_order_items)."
  - "The Add Order Item form on the supplier page captures an optional Batch #, saved with the item (both the header modal and the in-Edit-Order add form)."
  - "The order-items table shows a Batch # column in BOTH the supplier-view order panel and the All Orders detail view; items without one display blank."
  - The All Orders quick-search box also matches batch numbers.
  - Existing orders/items are unaffected (batch is optional, blank on legacy rows).
  - "Batch # on EXISTING line items can be set/edited inline from the Edit Order modal, saved via a new endpoint (supplier-update-order-item-batch); the on-page order panel + the modal list refresh on save."
  - The new edit endpoint is registered in mdm/admin RBAC and granted to the same role(s) that can manage the Suppliers page (otherwise non-superadmins 403).
out_of_scope:
  - Per-batch stock quantity tracking / FIFO depletion (aggregate total_stock stays as-is) — separate spike.
  - Linking quality failure points / quality checks to a specific batch — follow-up once batch numbers exist.
code_anchors:
  - path: sql/stock_supplier_order_items_batch_number.sql
    note: "NEW manual DDL: ALTER TABLE stock_supplier_order_items ADD batch_number VARCHAR(50) NULL AFTER description (run once; legacy rows stay NULL)"
  - path: ya-hire/common/models/StockSupplierOrderItems.php
    note: add batch_number to rules (string max 50) + attributeLabels + @property; beforeSave '' -> null
  - path: ya-hire/backend/controllers/StockController.php
    note: actionSupplierAddOrderItem already load()s batch once it's a safe rule (no change); serializeSupplierOrder -> include batch_number; NEW actionSupplierUpdateOrderItemBatch (edit existing item's batch, returns orderPanelHtml + serialized order like delete-order-item)
  - path: ya-hire/backend/views/stock/supplier-view.php
    note: "Batch # input on both add forms (header order-item-form + in-modal modal-order-item-form); edit-order items table Batch column + renderOrderItemsTable inline batch input & per-row save; wire supplierAjaxConfig.updateOrderItemBatch + click handler"
  - path: ya-hire/backend/views/stock/partials/_supplier-order-panel.php
    note: "order-items table -> add Batch # column (after Product); rebalance widths"
  - path: ya-hire/backend/views/stock/all-orders.php
    note: "detail order-items table -> add Batch # column; include batch_number in the row data-search string"
  - path: RBAC (mdm/admin, DB)
    note: register route stock/supplier-update-order-item-batch and grant to the same role(s) as stock/suppliers + stock/supplier-add-order-item (operational step in the admin UI; superusers work without it)
relates:
  - T-0652
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260724-0805
    model: claude-opus-4-8
    started: 2026-07-24T08:05:06Z
    status: in_progress
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-24T08:05:06Z
labels:
  - suppliers
  - orders
  - stock
  - quality-management
attention: null
version: 5
---

## Problem
Received stock can't be traced back to a specific batch. Quality failure points (T-0554) already have a free-text `batchRef`, but there's no real batch number captured anywhere on the orders/items they'd reference.

## Solution (small, high-value slice)
Add an optional **Batch #** to supplier order **line items** (a batch is per-product-per-delivery, so it belongs on the item, not the whole order). Capture it when adding an item, show it in the order detail on both the supplier-view page and the new All Orders page, and let the All Orders quick-search match it.

## Data
- New column `batch_number VARCHAR(50) NULL` on `stock_supplier_order_items` (manual DDL; existing rows stay NULL).

## Deferred (see out-of-scope)
- **Per-batch stock quantity tracking** (how many of each batch are in stock, FIFO) — the aggregate `total_stock` model doesn't support this; bigger piece, separate spike.
- **Wiring batch into quality** (failure points / checks referencing a real batch) — natural follow-up once batch numbers exist.

## Source
Chat 23 Jul (Zsolt) — spun out of the All Orders work (T-0557).