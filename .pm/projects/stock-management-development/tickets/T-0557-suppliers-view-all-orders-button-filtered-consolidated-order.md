---
id: T-0557
title: 'Suppliers: "View all orders" button → filtered consolidated orders list'
type: feature
state: in_progress
created: 2026-07-14T05:35:20Z
updated: 2026-07-23T09:38:33Z
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
  - path: ya-hire/common/models/StockSupplierOrders.php
    note: "the placed-orders model (table stock_supplier_orders) — status values Pending/Ordered/In Transit/Received/Cancelled; supplier FK, order_date, arrival_date, totals. Line items: StockSupplierOrderItems (stock_supplier_order_items)."
  - path: ya-hire/backend/controllers/StockController.php
    note: actionSuppliers (~L8507) renders the suppliers page; add new actionAllOrders() (consolidated filtered list). serializeSupplierOrder() + order status options + colour map already exist to reuse.
  - path: ya-hire/backend/views/stock/suppliers.php
    note: suppliers page — add 'View all orders' button at top next to 'Add Supplier' (~L24).
  - path: ya-hire/backend/views/stock/all-orders.php
    note: NEW page — filter bar (status/supplier/date/order#) + consolidated orders table with expandable line-item rows, grouped by status priority.
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
version: 5
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

## Conversation

**2026-07-23 09:38 claude-code:** **2026-07-23 — Open questions resolved + build decisions (Zsolt):**

**Where orders live:** `stock_supplier_orders` (model `StockSupplierOrders`) + line items in `stock_supplier_order_items`, linked to `stock_suppliers`. NOT upcoming-stock (that page is a separate stock-forecasting tool and holds no placed orders) — correcting that code-anchor.

**Status values (fixed):** Pending · Ordered · In Transit · Received · Cancelled (colours already defined in `_supplier-order-panel.php`).

**Decisions:**
- **Presentation:** a dedicated new page (`/stock/all-orders`) opened by the button — not a modal/inline section.
- **Order detail:** expandable rows (click a row to reveal its line items inline).
- **Filters:** all four — Status, Supplier, Date range (order date From/To), and Search by order number.
- **Grouping/sort:** status priority **In Transit → Ordered → Pending → Received → Cancelled**, newest first within each.
- **Filtering mechanism:** server-side via a GET form (bookmarkable URL); row expand is client-side.
- **Button:** top of the Suppliers page next to "Add Supplier".
- **Permissions:** gate with the same access as the Suppliers page.

Out of scope (stays in the spike): the bigger single "Orders" management page / decision board, edit-in-place, merging with upcoming/buffer. This ticket = button + filtered read-only list only.
