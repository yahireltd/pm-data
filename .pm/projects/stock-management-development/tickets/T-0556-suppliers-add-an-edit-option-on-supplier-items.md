---
id: T-0556
title: "Suppliers: add an Edit option on supplier items"
type: feature
state: review
created: 2026-07-14T05:35:11Z
updated: 2026-07-20T09:03:59Z
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
  - "[x] Each supplier item (StockSupplierItems) on the suppliers page has an Edit control."
  - "[x] Editing opens a form/modal to change the item's fields (confirm exact fields on pickup — e.g. name/description/link/qty/price) and save."
  - "[x] Changes persist and are reflected immediately on the page."
  - "[x] Edit is gated to the same staff role that manages suppliers today."
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
attention:
  needed_by: human
  reason: Inline edit for supplier items implemented and committed (40a71e08, branch Stock-Management-Development). Ready for verification and close against the posted test plan — remaining checks are the two edge cases (already-linked product, clear-both validation) and the added-row Edit-icon cross-impact.
  since: 2026-07-20T09:01:18Z
version: 10
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

## Conversation

**2026-07-20 08:24 claude-code:** **Implemented (working tree — not committed, per project no-commit policy).**

Added an **Edit** control to each supplier item, mirroring the existing supplier *contact* edit flow. Editable fields (confirmed against the `stock_supplier_items` schema): **linked YA product**, **Supplier Reference**, **Price** (there is no separate URL/link field on the model).

Changes across the three code anchors:
- **StockController** — new `actionSupplierUpdateItem` (route `supplier-update-item`), a copy of the add/update-contact pattern: finds the item by link_id + supplier_id, loads + saves, returns the re-rendered row. Gating is identical to Add/Delete item (no per-action RBAC in this controller — same access as managing suppliers today), so AC #4 holds.
- **partials/_supplier-item-row.php** — gave the row an id and added a blue pencil Edit icon (with the item's current values as data-attributes) alongside the delete icon.
- **supplier-view.php** — new "Edit Item" modal (product autocomplete → `ajax/all-items-search`, reference, price) + JS to populate it from the row, submit over AJAX, and swap the updated row in place.

Persists immediately with no page reload. All three files pass `php -l`.

**Test plan**
1. Stock → Suppliers → open a supplier. Each Supplier Items row now shows a pencil Edit icon.
2. Edit → modal opens pre-filled with the item's product / reference / price.
3. Change **price** → Save → row updates in place (no reload).
4. Change **supplier reference** → Save → row updates.
5. Change the **linked product** via autocomplete → Save → row shows the new product name.
6. Clear the product link (leave only a reference) → Save → row shows the reference.
7. Edge — link a product already linked to this supplier → Save returns "This product is already linked to the supplier."
8. Edge — clear both product and reference → Save returns the validation error.
9. Cross-impact — `_supplier-item-row.php` is shared with **Add**: confirm newly-added rows also get a working Edit icon, and **Delete** still works.
10. Confirm a supplier-managing user can edit (same access as Add/Delete).

**2026-07-20 09:01 claude-code:** **Committed & moving to review.**

Committed by Zsolt as `40a71e08` — "Add inline editing for supplier items" (branch `Stock-Management-Development`).

Implementation is complete (see the earlier comment for the full test plan). Edit icon confirmed present and basic save verified. Before closing to **done**, recommend a quick pass on the parts not yet exercised:
- Link a product **already linked** to the supplier → expect "This product is already linked to the supplier."
- **Clear both** product and reference → expect the validation error.
- Confirm a **newly-added** row also gets a working Edit icon (shared `_supplier-item-row` partial — the one real cross-impact).

Handing to a human to verify and close.
