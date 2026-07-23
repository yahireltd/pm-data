---
id: T-0652
title: Suppliers area UI modernisation (Suppliers, All Orders, Supplier View)
type: chore
state: triaged
created: 2026-07-23T12:55:09Z
updated: 2026-07-23T12:55:16Z
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
  - Suppliers, All Orders and Supplier View pages share a consistent modern card design (rounded cards, soft shadow, clean headers).
  - Purchasing stat cards on Suppliers + All Orders (+ per-supplier on Supplier View); order cards link through to All Orders with the right filter (status / overdue / supplier).
  - Add Contact, Link Product, Create Order and Add Order Item are modals opened from panel-header buttons; the panels themselves just show the lists.
  - Consistent tables (borderless + hover), pill count badges, pill action buttons, and plain-text empty states across the three pages.
out_of_scope:
  - Batch numbers on order items (separate ticket T-0651).
  - Functional/behavioural changes to orders/contacts/items beyond layout + modal wrapping.
code_anchors:
  - path: ya-hire/backend/views/stock/suppliers.php
    note: "DONE: 6 stat cards (+ sub-lines, order cards link to all-orders), modern card table, initials avatars, count pills, pill action buttons, paginated Supplier Items (25/pg), centered Contacts/Items/Orders/Actions"
  - path: ya-hire/backend/views/stock/all-orders.php
    note: "DONE: card styling + header, stat cards that double as status filters (All/On The Way/Overdue/Delivered/Total Value), quick-search, left status stripe rows"
  - path: ya-hire/backend/views/stock/supplier-view.php
    note: "DONE: panels->cards CSS, modern header, per-supplier stat cards (linked to all-orders?supplier_id=), Email/Phone 50/50 details grid + Notes, Contacts/Products-Linked moved out, Add Contact + Link Product + Create Order + Add Order Item now MODALS with header buttons; counts update + modals close on save"
  - path: ya-hire/backend/controllers/StockController.php
    note: "DONE: shared supplierOrderStats() helper (used by suppliers + all-orders); actionAllOrders supports ?overdue=1 and ?supplier_id filters"
  - path: ya-hire/backend/views/stock/partials/_supplier-order-panel.php
    note: "DONE: fixed blank Product column (missing echo)"
relates:
  - T-0557
  - T-0651
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - suppliers
  - orders
  - stock
  - ui
attention: null
version: 2
---

## Goal
Modernise the whole suppliers area so Suppliers, All Orders and Supplier View read as one consistent purchasing dashboard. Grew out of T-0557 (All Orders) — tracked separately since it's a broad UI refresh, not the "View all orders" feature itself.

## Done so far (2026-07-23, working tree, lint clean)
- **Suppliers page**: 6 stat cards (Suppliers / Open / On The Way / Overdue / Delivered / Total Spent) with £ + items/units/avg sub-lines; order cards link to All Orders. Card-based table, initials avatars, colour count pills, pill action buttons, paginated Supplier Items (25/pg), centered numeric/action columns.
- **All Orders page**: card styling + header; stat cards that double as **status filters** (All / On The Way / Overdue / Delivered / Total Value) via `?status=` + new `?overdue=1`; quick-search; left status-colour stripe per row; expandable detail matching the order panel.
- **Supplier View page**: Bootstrap panels restyled to cards; modern header; per-supplier stat cards (link to `all-orders?supplier_id=`); Supplier Details slimmed to Email/Phone (50/50) + Notes; Products Linked moved to a top card, Contacts count shown in its panel header; **Add Contact, Link Product, Create Order, Add Order Item all converted to modals** with header buttons (counts refresh + modals close on save); plain-text empty states.
- **Shared**: `supplierOrderStats()` controller helper; fixed a blank Product column bug in the order-panel partial.

All read-only / layout only. **No schema changes.** Working-tree only (no commit/push per project policy).

## Remaining (pick up next)
- Unify the **Contacts + Items tables** on Supplier View to the cleaner borderless+hover style (still `table-bordered table-striped`).
- Match the **row Edit/Delete buttons + "Edit Order" button** to the new pill button style.
- Modernise the **individual order panels** (the purple-accented per-order panels are the most dated area).
- Align the **Edit Item modal** help text with the corrected Link Product copy ("we'll use the typed name" is still slightly misleading there).

## Source
Chat 23 Jul (Zsolt) — iterative design pass alongside T-0557.