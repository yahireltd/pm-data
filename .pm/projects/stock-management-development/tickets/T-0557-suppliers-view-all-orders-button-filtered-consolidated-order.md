---
id: T-0557
title: 'Suppliers: "View all orders" button → filtered consolidated orders list'
type: feature
state: in_progress
created: 2026-07-14T05:35:20Z
updated: 2026-07-14T06:31:26Z
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
  - The suppliers page has a 'View all orders' button that opens a consolidated list of all placed orders.
  - The list has filter options (e.g. by status, supplier, and/or date).
  - Orders are grouped/sorted usefully — e.g. Ordered and In-transit surfaced at the top, Received lower down.
  - Existing per-supplier order handling is unchanged; this is an additional cross-supplier view.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/views/stock/suppliers.php
    note: suppliers page — add the 'View all orders' button
  - path: ya-hire/backend/controllers/StockController.php
    note: actionSuppliers (~L8345) — add a consolidated-orders action + filter query
  - path: ya-hire/backend/views/stock/upcoming-stock.php
    note: current source of order/upcoming data — where placed orders live today
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
attention: null
version: 3
---

## Source
Kickoff M-001: "On suppliers page add a view all button to display all orders (with filter options)."

## Scope (kept to the defined quick-win)
Just the **button + filtered consolidated list** of placed orders, with sensible grouping (Ordered / In-transit on top, Received at the bottom).

## Out of scope (see the spike)
The bigger single **"Orders" management page / product-decision board** (edit-in-place, decision workflow, merging with upcoming/buffer) is conceptual and tracked separately as a spike.

## Open questions (confirm on pickup)
- Where "orders" live today (upcoming-stock vs a supplier-orders table) and their status values.
- Which filters matter most (status / supplier / date).
