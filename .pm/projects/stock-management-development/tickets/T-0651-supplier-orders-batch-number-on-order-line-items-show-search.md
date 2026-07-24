---
id: T-0651
title: "Supplier orders: batch number on order line items (+ show/search)"
type: feature
state: done
created: 2026-07-23T10:58:21Z
updated: 2026-07-24T11:47:41Z
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
  - "[x] Supplier order line items have an optional Batch # field (new nullable column batch_number on stock_supplier_order_items)."
  - "[x] The Add Order Item form on the supplier page captures an optional Batch #, saved with the item (both the header modal and the in-Edit-Order add form)."
  - "[x] The order-items table shows a Batch # column in BOTH the supplier-view order panel and the All Orders detail view; items without one display blank."
  - "[x] The All Orders quick-search box also matches batch numbers."
  - "[x] Existing orders/items are unaffected (batch is optional, blank on legacy rows)."
  - "[x] Batch # on EXISTING line items can be set/edited inline from the Edit Order modal, saved via a new endpoint (supplier-update-order-item-batch); the on-page order panel + the modal list refresh on save."
  - "[x] The new edit endpoint is registered in mdm/admin RBAC and granted to the same role(s) that can manage the Suppliers page (otherwise non-superadmins 403)."
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
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-24T08:05:06Z
    progress:
      - at: 2026-07-24T08:10:52Z
        note: |-
          Built the full batch-number feature incl. edit-on-existing (working tree, all files php -l clean). No commit per policy.

          Data: new `sql/stock_supplier_order_items_batch_number.sql` — ALTER adds `batch_number VARCHAR(50) NULL` after `description`. **User runs it once** (legacy rows stay NULL).

          Model (StockSupplierOrderItems): batch_number added to @property, rules (string max 50), attributeLabels ('Batch #'), and beforeSave ('' -> null).

          Controller (StockController):
          - serializeSupplierOrder now includes batch_number in each item payload (feeds the Edit Order modal table).
          - actionSupplierAddOrderItem needed NO change — it load()s the model, so batch saves automatically now it's a safe rule (covers both add forms).
          - NEW actionSupplierUpdateOrderItemBatch — validates the item belongs to the supplier, sets batch (blank->null), save(false, ['batch_number']), returns orderPanelHtml + orderId + serialized order (same shape as delete-order-item) so panel + modal both refresh.

          Views:
          - _supplier-order-panel.php + all-orders.php: Batch # column (after Product) in the order-items tables; all-orders also adds batch to each row's data-search so quick-search matches batch numbers.
          - supplier-view.php: Batch # input on BOTH add forms (header order-item-form + in-Edit-Order modal-order-item-form); Batch # column in the edit-order items table; renderOrderItemsTable renders the Batch cell as an inline <input> pre-filled from item.batch_number; new delegated change handler on .modal-order-item-batch posts to updateOrderItemBatch (wired into supplierAjaxConfig) and refreshes on save. Column widths rebalanced in all three tables.

          TWO MANUAL STEPS before it works for everyone:
          1. Run the SQL (adds the column).
          2. RBAC: stock/* is gated by mdm/admin AccessControl (not in allowActions), so the new route `stock/supplier-update-order-item-batch` must be registered + granted to the same role(s) as stock/suppliers / stock/supplier-add-order-item, else non-superadmins 403. (Superusers work immediately.)

          Not yet verified live (needs the column + auth). Add/existing-edit paths + quick-search covered in the test plan.
    ended: 2026-07-24T08:27:07Z
    summary: |-
      Added the ability to record a batch (lot) number against each item on a supplier order. Until now received stock couldn't be traced back to a specific batch — the quality failure-points already had a free-text batch reference, but there was no actual batch number captured anywhere on the orders themselves.

      Now, when someone adds an item to a supplier order they can type an optional Batch #, and it's shown alongside that item wherever the order is viewed — on the supplier's page and on the consolidated All Orders page. Staff can also fill in or change the batch on items that were already recorded, straight from the order's edit pop-up, so existing orders can be back-filled. The All Orders search box also finds orders by batch number.

      Why it matters: batch numbers are the link between a delivery and any quality issue found later. Capturing them here is the foundation for tracing problems back to a specific batch. It's an optional field — existing orders are completely unaffected and simply show blank until a batch is entered. Nothing about prices, quantities or stock levels changed.
    test_plan: |-
      Two setup steps first, then the checks. Working tree only; no commit.

      SETUP (required — the feature is invisible until both are done):
      1. Run the SQL once: `sql/stock_supplier_order_items_batch_number.sql` (adds the `batch_number` column).
      2. RBAC: grant the new route `stock/supplier-update-order-item-batch` to the same role(s) that can manage suppliers (Admin → Route Access), mirroring `stock/supplier-add-order-item`. Superusers work without this; other roles will 403 on the inline batch-edit until granted. (Ask claude-code to identify the exact role if unsure.)

      CHECKS (on Supplier View for a supplier with an order, + All Orders):
      1. Add via header modal: Suppliers → open a supplier → "Add Order Item" → fill an item incl. a Batch # → Save. The order panel expands to show the new item with its Batch # in the Batch column.
      2. Add via Edit Order: open an order's "Edit Order" → "Add Item to Order" → include a Batch # → Add. Same result.
      3. Edit existing item's batch: in the Edit Order modal, the items list shows each batch as an editable box. Type/clear a batch and click away (blur). It saves; the order panel behind refreshes and shows the new value. Reopen the order to confirm it persisted.
      4. Blank case: an item with no batch shows an empty Batch cell everywhere (supplier panel + All Orders detail).
      5. All Orders column: open an order row on /stock/all-orders → the detail table shows the Batch # column.
      6. All Orders search: type a batch number into the quick-search box → orders containing that batch stay visible, others hide.
      7. Legacy safety: existing items (pre-SQL) show blank batch and are otherwise unchanged; order subtotals/grand totals are unchanged throughout.
      8. Cross-impact: the order panel partial is shared + AJAX-rendered (add/delete item, batch edit) — confirm add item, delete item, and status/order edits still work normally.

      Docs: docs/features/stock-supplier-order-batch-numbers.md.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: New feature doc docs/features/stock-supplier-order-batch-numbers.md (T-0651); also cleared a TODO in stock-suppliers-ui-modernisation.md by adding the All Orders filtered-summary section + cross-link.
labels:
  - suppliers
  - orders
  - stock
  - quality-management
attention: null
version: 19
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

## Conversation

**2026-07-24 08:27 claude-code:** Run run-20260724-0805 completed — Added the ability to record a batch (lot) number against each item on a supplier order. Until now received stock couldn't be traced back to a specific batch — the quality failure-points already had a free-text batch reference, but there was no actual batch number captured anywhere on the orders themselves.

Now, when someone adds an item to a supplier order they can type an optional Batch #, and it's shown alongside that item wherever the order is viewed — on the supplier's page and on the consolidated All Orders page. Staff can also fill in or change the batch on items that were already recorded, straight from the order's edit pop-up, so existing orders can be back-filled. The All Orders search box also finds orders by batch number.

Why it matters: batch numbers are the link between a delivery and any quality issue found later. Capturing them here is the foundation for tracing problems back to a specific batch. It's an optional field — existing orders are completely unaffected and simply show blank until a batch is entered. Nothing about prices, quantities or stock levels changed.

---

**2026-07-24 11:47 — you**

done
