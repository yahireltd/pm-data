---
id: T-0557
title: 'Suppliers: "View all orders" button → filtered consolidated orders list'
type: feature
state: review
created: 2026-07-14T05:35:20Z
updated: 2026-07-23T12:55:24Z
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
relates:
  - T-0652
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - suppliers
  - orders
  - stock
attention:
  needed_by: human
  reason: |-
    Built and iterated with Zsolt: Suppliers page "View all orders" button → new /stock/all-orders page (status-grouped, expandable rows matching the supplier-view detail, single quick-search box). Working-tree only, lint clean, no schema changes. Test plan below. Ready for review + close.

    Test plan:
    1. Suppliers page → click "View all orders" → lands on /stock/all-orders.
    2. Confirm orders are grouped by status (In Transit → Ordered → Pending → Received → Cancelled), each group with a colour band + column headers; rows uniform height with a status-coloured left stripe.
    3. Click an order row → it expands; header takes a faded tint of its own status colour, body white; detail shows Order/Arrival dates + Subtotal/Grand Total, Notes, and the Description/Product/Qty/Unit Price/Line Total table (matches the supplier-view panel for the same order).
    4. Quick-search box: type an order number, a company, a status (e.g. "received"), an item name, a date ("may 2026"), and a total both ways ("1500" and "1,500") — each should filter correctly and hide empty status groups.
    5. Resize/filter and confirm column widths stay fixed (don't jump).
    6. Cross-check: open the SAME order on the supplier-view page and confirm the expanded detail looks the same, and that the Product column there now shows (previously blank — bug fixed in _supplier-order-panel.php; re-check the supplier-view orders section still renders/edits fine).
    7. Confirm existing per-supplier order handling on supplier-view is unchanged (add order, add item, status update, delete).
  since: 2026-07-23T11:18:51Z
version: 10
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

**2026-07-23 11:18 claude-code:** **2026-07-23 — Built (working tree, lint clean). Moving to review.**

**What shipped**
- New **"View all orders"** button on the Suppliers page → new dedicated page `/stock/all-orders` (`actionAllOrders`).
- Read-only consolidated list of every supplier order, **grouped by status** (In Transit → Ordered → Pending → Received → Cancelled), each group with a colour band + its own column-header row (Order No · Company · Status · Order Date · Arrival Date · Items · Total).
- **Expandable rows** — click an order to reveal its detail; the detail now **matches the supplier-view panel exactly** (Order/Arrival dates + Subtotal/Grand Total, Notes, and a Description · Product · Qty · Unit Price · Line Total table).
- **One quick-search box** (client-side) matching order #, company, status, item (description + product), dates, and the order total — replacing the original heavy server-side filter bar per your steer.

**UI polish from review passes**
- Left status-colour stripe per row (white rows for readability); expanded order highlighted with a faded version of *its own* status colour (green/blue/etc, not always blue) and a white detail body.
- Inactive-check styling untouched (separate feature).
- Fixed column widths (table-layout:fixed + colgroup) so columns don't jump when filtering; Status/Order Date/Arrival Date centered.
- Uniform 34px row height — root cause was the `—` em-dash placeholder pulling a taller fallback font; swapped to a plain dash.

**Bug fixed along the way:** the supplier-view order panel showed a blank **Product** column (its partial called `Html::encode()` with no `echo`) — corrected in `_supplier-order-panel.php`, so both views now show the product.

**Delivery:** working-tree only (no commit/push per project policy). No schema changes.

Related follow-up already logged: **T-0651** (batch numbers on order line items) — parked to do next.

**2026-07-23 11:47 claude-code:** **2026-07-23 — Follow-up: modernised the Suppliers + All Orders pages into a purchasing dashboard (working tree, lint clean).**

Beyond the original scope, gave both pages a design refresh and a stats layer:

**Suppliers page**
- Modern card-based layout: header with live subtitle, rounded card-wrapped tables, hover rows, initials avatars per supplier, count columns as colour-coded pills (Contacts/Items/Orders), soft pill action buttons.
- **6 summary stat cards**: Suppliers · Open Orders · On The Way · Overdue (red when >0) · Delivered · Total Spent — each with a secondary line (£ value on the order cards, linked-items on Suppliers, units on Delivered, avg/order on Total Spent). The order cards link through to the All Orders page.
- Supplier Items table now paginated (25/page). Centered the Contacts/Items/Orders/Actions columns.

**All Orders page**
- Same card styling + header ("N total orders · £X").
- **Stat cards that double as status filters**: All Orders · On The Way · Overdue · Delivered · Total Value — each shows count + £, the active one highlighted, click to filter the list (uses `?status=` / new `?overdue=1`); a "clear" link resets. Replaces the old per-status chip line; quick-search still narrows within the shown set.

**Refactor:** the dashboard numbers come from one shared `supplierOrderStats()` controller helper used by both pages (no duplicated queries). All read-only, no schema changes.

Files touched: `StockController.php`, `views/stock/suppliers.php`, `views/stock/all-orders.php`. Still working-tree only per project policy.

Note: a **batch-number** follow-up (T-0651) remains parked to do next.

**2026-07-23 12:55 claude-code:** **2026-07-23 — The broader UI modernisation (Suppliers + Supplier View pages, modal conversions, shared stat helper) has been split out to its own ticket T-0652** so this one stays scoped to the "View all orders" feature. T-0557's own work (the All Orders page + its stat/filter cards) is unchanged and still in review. See T-0652 for the full done/remaining list.
