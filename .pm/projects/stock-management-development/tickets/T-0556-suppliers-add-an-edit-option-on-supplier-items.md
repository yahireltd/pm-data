---
id: T-0556
title: "Suppliers: add an Edit option on supplier items"
type: feature
state: in_progress
created: 2026-07-14T05:35:11Z
updated: 2026-07-14T06:31:22Z
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
  channel: kickoff M-001
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - Each supplier item (StockSupplierItems) on the suppliers page has an Edit control.
  - Editing opens a form/modal to change the item's fields (confirm exact fields on pickup — e.g. name/description/link/qty/price) and save.
  - Changes persist and are reflected immediately on the page.
  - Edit is gated to the same staff role that manages suppliers today.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: actionSuppliers (~L8345) + supplier item handling (StockSupplierItems) — add edit + save actions (mirror actionSaveSupplierInfo ~L8295)
  - path: ya-hire/backend/views/stock/supplier-view.php
    note: supplier items list — add the Edit control + edit form/modal
  - path: ya-hire/common/models/StockSupplierItems.php
    note: supplier-item model — fields to edit + save
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - suppliers
  - stock
attention: null
version: 3
---

## Source
Kickoff M-001 (Ben's document): "Suppliers, edit option on supplier items, should be easy enough — option will be added to the supplier items."

## Problem
Supplier items can be added/removed but not **edited** — a typo or a changed price/link means delete + re-add.

## What's wanted
- An **Edit** control on each supplier item, opening a form/modal to change its fields and save.

## Open questions (confirm on pickup)
- Exact editable fields on `StockSupplierItems` (name/description/link/price/qty?).
- Reuse the existing supplier-info modal pattern (`actionSaveSupplierInfo`) for consistency.
