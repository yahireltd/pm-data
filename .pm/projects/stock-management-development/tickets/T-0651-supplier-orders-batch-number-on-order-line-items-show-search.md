---
id: T-0651
title: "Supplier orders: batch number on order line items (+ show/search)"
type: feature
state: triaged
created: 2026-07-23T10:58:21Z
updated: 2026-07-23T10:58:21Z
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
assignee: null
acceptance_criteria:
  - "Supplier order line items have an optional Batch # field (new nullable column batch_number on stock_supplier_order_items)."
  - "The Add Order Item form on the supplier page captures an optional Batch #, saved with the item."
  - "The order-items table shows a Batch # column in BOTH the supplier-view order panel and the All Orders detail view; items without one display blank."
  - The All Orders quick-search box also matches batch numbers.
  - Existing orders/items are unaffected (batch is optional, blank on legacy rows).
out_of_scope:
  - Per-batch stock quantity tracking / FIFO depletion (aggregate total_stock stays as-is) — separate spike.
  - Linking quality failure points / quality checks to a specific batch — follow-up once batch numbers exist.
code_anchors:
  - path: ya-hire/common/models/StockSupplierOrderItems.php
    note: add batch_number to rules + attributeLabels (varchar 50, nullable)
  - path: ya-hire/backend/controllers/StockController.php
    note: actionSupplierAddOrderItem — read/save batch_number; serializeSupplierOrder — include batch_number in the item payload
  - path: ya-hire/backend/views/stock/supplier-view.php
    note: "Add Order Item form — add a Batch # input; item row rendering (JS template) — show batch"
  - path: ya-hire/backend/views/stock/partials/_supplier-order-panel.php
    note: "order-items table — add a Batch # column"
  - path: ya-hire/backend/views/stock/all-orders.php
    note: "order-items table — add Batch # column; include batch in the quick-search data-search string"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - suppliers
  - orders
  - stock
  - quality-management
attention: null
version: 1
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