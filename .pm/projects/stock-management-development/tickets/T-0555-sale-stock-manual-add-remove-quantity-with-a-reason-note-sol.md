---
id: T-0555
title: "Sale stock: manual add/remove quantity with a reason note (Sold / Binned / contract #) + audit log"
type: feature
state: done
created: 2026-07-14T05:32:53Z
updated: 2026-07-20T12:49:53Z
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
  - "[x] On the sale-stock screen, a staff member can manually adjust (add OR remove) an item's sale-stock quantity."
  - "[x] Each manual adjustment requires a reason note (e.g. Sold / Binned) and can optionally include a contract number for the record."
  - "[x] Every adjustment is logged: who, when, +/- amount, reason note, contract number — an audit trail viewable against the item."
  - "[x] Existing automatic behaviour is unchanged: quote→contract conversion still deducts the sale-stock qty, and re-adding the item still tops the number up."
  - "[x] Validation prevents nonsensical results (e.g. no silent negative stock without an explicit reason)."
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: actionStockForSale (~L7235), actionEditSaleItemModal (~L7284), actionUpdateSaleItem (~L7347) — add manual qty adjust + reason
  - path: ya-hire/common/models/StockLevelsForSale.php
    note: sale-stock model (updateSaleItem) — apply the +/- adjustment
  - path: ya-hire/backend/views/stock/stock-for-sale.php
    note: sale-stock screen — add the adjust control + reason/contract note UI
  - path: ya-hire/common/models
    note: "NEW adjustments log table: saleItemID, delta, reason, contractNo, userID, createdAt"
relates:
  - T-0624
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sale-stock
  - stock
attention: null
version: 18
---

## Source
Kickoff M-001 (Sandor's document). Sale-stock quick-win in Milestone MS-001.

## Problem
Sale-stock quantity only changes automatically: adding the item to a quote and converting to a contract deducts it, and adding the item again tops it up. But if sales add **text items** (not the actual sale-stock line) on the Q/C, it **doesn't deduct** — and there's **no way to manually correct** the number. Sandor needs to add/remove qty by hand.

## What's wanted
- A **manual add/remove** control on the sale-stock screen.
- Each change carries a **note** — *Sold*, *Binned*, and optionally a **contract number** for the record.
- Keep an **audit trail** of adjustments.
- Ben's steer: "Go with what you think is best that Sandor agrees — I'm sure he'll do it right."

## Open questions (confirm on pickup)
1. Should removing below zero be blocked, or allowed with a forced reason?
2. Where the audit log is shown (inline on the item, or a small history modal?).
3. Fixed reason options (Sold / Binned / Other-with-text) vs free text.

## Notes
- Leave the automatic quote→contract deduction path untouched; this is an additive manual override + log.

## Conversation

**2026-07-20 09:44 claude-code:** **Decisions agreed (with Zsolt, 2026-07-20)** — resolves the ticket's open questions:
1. **Never go below zero.** A removal can't take sale stock negative — it's rejected with an error (negative sale stock makes no sense).
2. **Audit trail** shown in a per-item **History** modal.
3. **Reason** = dropdown **Sold / Binned / Other** (+ a free-text note, required when "Other"); **contract number** optional.
4. Manual Sold/Binned adjustments are logged to the **new audit table only** — they do **not** write a stock-transaction, because an item on the sale list was already transferred off normal stock, so a correction shouldn't move hire stock.

**Implementation plan**
- **New table `stock_sale_adjustments`** (+ model + migration): `saleItemID`, `stockID`, `delta` (signed +add/−remove), `reason`, `note`, `contractNo`, `qtyBefore`, `qtyAfter`, `userID`, `createdAt`.
- **Adjust logic** (`StockLevelsForSale::adjustSaleItem`): validate qty>0, reason present (note required for Other), and **new qty ≥ 0** (else reject); in a transaction update the stock level and insert the audit row. No stock-transaction written.
- **Controller:** `actionAdjustSaleItem` (do the adjust) + `actionViewSaleItemAdjustments` (render the history — mirrors the existing price-changes history modal).
- **UI (`stock-for-sale.php`):** per-row **Adjust Qty** button (Add/Remove, Qty, Reason, Note, Contract #) + **History** link; two modals + JS. The existing "Add More Qty (transfer from hire)" path and the automatic quote→contract deduction are left untouched.
- **Access:** both new routes need granting to the sale-stock role in RBAC (mdm AccessControl is deny-by-default for new routes) — flagged so it isn't missed.
- **Docs** added per the house convention.

**To confirm with Sandor (non-blocking):** (a) the new "Adjust Qty" and the existing "Add More Qty (transfer)" will coexist — OK, or make Adjust the single +/- entry point? (b) contract # as free text vs validated against a real contract — starting with free text.

Building now on branch `Stock-Management-Development`; migration will be written but not run (project no-commit policy) — it'll need `yii migrate` + the RBAC route grant to go live.

**2026-07-20 10:10 claude-code:** **Implemented (working tree — not committed, per project no-commit policy).** Branch `Stock-Management-Development`. All changed PHP passes `php -l`.

**New files**
- `console/migrations/m260720_100000_create_stock_sale_adjustments_table.php` — creates `stock_sale_adjustments`.
- `common/models/StockSaleAdjustment.php` — the audit-log model.
- Docs: `actionAdjustSaleItem.md`, `actionViewSaleItemAdjustments.md`.

**Edited**
- `StockLevelsForSale::adjustSaleItem()` — validates, applies +/- delta, **blocks below zero**, and logs the audit row in a transaction. No `stock_transactions` write.
- `StockController` — `actionAdjustSaleItem` + `actionViewSaleItemAdjustments` (+ `use` import).
- `views/stock/stock-for-sale.php` — per-row **Adjust Qty** + **History** icons, an Adjust modal (Add/Remove, Qty, Reason Sold/Binned/Other, Note-for-Other, Contract #), a History modal, and their JS.

**⚠️ Two ops steps before it works live** (won't function until both are done):
1. **Run the migration** — `yii migrate` (creates the table).
2. **RBAC** — grant the two new routes `stock/adjust-sale-item` and `stock/view-sale-item-adjustments` to the sale-stock role (or confirm they're covered by a `stock/*` wildcard). Deny-by-default, same as the T-0556 gotcha.

**Test plan**
1. Sale-stock screen → each row has a teal **Adjust** (↔) icon and a grey **History** (clock) icon.
2. Adjust → **Remove** some qty with reason **Sold** → save → qty drops by that amount; History shows the row (−N, before→after, Sold, user, time).
3. **Add** with reason **Binned**/**Other** → qty rises; **Other** requires a note (blocked without one).
4. **Below-zero block:** remove more than current qty → rejected with "Cannot remove N — only X in stock"; qty unchanged; no audit row.
5. Optional **contract #** is recorded and shown in History.
6. **Regression:** the existing "Add More Qty (transfer from hire / add off-stock)" in the Edit modal still works and still writes a `StockTransactions` row; automatic quote→contract deduction unaffected.
7. **RBAC:** a sale-stock user (non-superadmin) can Adjust/History once the routes are granted; before granting, expect 403.

**To confirm with Sandor (non-blocking):** the new Adjust and the existing "Add More Qty (transfer)" coexist; contract # is free text for now.

**2026-07-20 10:51 claude-code:** **Correction to the ops steps:** the Yii migration was removed — the table will be created by **manual SQL** instead (Zsolt's preference). So step 1 is *run the DDL*, not `yii migrate`. DDL (default `db` connection, same as `stock_levels_for_sale`):

```sql
CREATE TABLE `stock_sale_adjustments` (
  `id`         INT          NOT NULL AUTO_INCREMENT,
  `saleItemID` INT          NOT NULL COMMENT 'stock_levels_for_sale.id',
  `stockID`    INT          NULL     COMMENT 'ya_products.id',
  `delta`      INT          NOT NULL COMMENT 'signed: + add, - remove',
  `qtyBefore`  INT          NOT NULL,
  `qtyAfter`   INT          NOT NULL,
  `reason`     VARCHAR(20)  NOT NULL COMMENT 'Sold | Binned | Other',
  `note`       VARCHAR(255) NULL,
  `contractNo` VARCHAR(50)  NULL,
  `userID`     INT          NOT NULL,
  `createdAt`  DATETIME     NOT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_stock_sale_adjustments_saleItemID` (`saleItemID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

Step 2 (RBAC route grant) unchanged.

**2026-07-20 10:52 claude-code:** **Progress:** ✅ Step 1 done — `stock_sale_adjustments` table created (DDL run manually by Zsolt).

Remaining before this is verifiable/live:
- **Step 2 — RBAC:** grant `stock/adjust-sale-item` + `stock/view-sale-item-adjustments` to the sale-stock role (or confirm `stock/*` covers them).
- **Commit** the code (working tree, no-commit policy).
- **Verify** against the test plan above (remove-with-reason logs it, below-zero blocked, "Other" needs a note, History modal, existing transfer path unaffected).

**2026-07-20 11:44 claude-code:** **RBAC (Step 2) — resolved which grants need the new routes.**

Checked what currently grants the existing sale-stock routes:
- **8 grants use the `/stock/*` wildcard** (`Product Manager` role, `Warehouse Controller`, `Managers`, `Accounts`, and the individual Gary/Jay/Thanos/Trudy permissions) → they **already auto-cover** `stock/adjust-sale-item` and `stock/view-sale-item-adjustments`. Nothing to do.
- **`Sales Permissions`** grants **explicit routes** (`stock-for-sale`, `update-sale-item`, `delete-sale-item`), not the wildcard → the two new routes must be added or Sales users 403 on Save/History.

Routes are stored with a leading slash. SQL to add them (safe to re-run):
```sql
INSERT IGNORE INTO auth_item (name, type, created_at, updated_at) VALUES
  ('/stock/adjust-sale-item', 2, UNIX_TIMESTAMP(), UNIX_TIMESTAMP()),
  ('/stock/view-sale-item-adjustments', 2, UNIX_TIMESTAMP(), UNIX_TIMESTAMP());

INSERT IGNORE INTO auth_item_child (parent, child) VALUES
  ('Sales Permissions', '/stock/adjust-sale-item'),
  ('Sales Permissions', '/stock/view-sale-item-adjustments');
```
(If RBAC is cached, clear the auth cache after.) Open question: Sales currently has view/update/delete but **not** `add-sale-item` — confirm adjusting sale-stock qty is acceptable for the Sales role before granting.

**Also note — extra scope shipped on this page** beyond the ticket's ACs: a page facelift (matches commission-thresholds design), an image lightbox modal, and Adjust-modal layout/validation polish (Sold ⇒ contract required, Other ⇒ note required, spacing/alignment fixes).

**Remaining to close:** run the RBAC SQL for Sales (if wanted), commit the working-tree code + docs, and verify a real Adjust saves + logs + shows in History.

**2026-07-20 11:56 claude-code:** **Committed & moving to review.** Commit `2c6af253` — "Add sale stock adjustment audit workflow" (branch `Stock-Management-Development`).

RBAC reminder for the reviewer: the two new routes must be granted to **Sales Permissions** for Sales users to use the buttons (the `/stock/*` roles are already covered):
- `/stock/adjust-sale-item` (Adjust Qty → Save)
- `/stock/view-sale-item-adjustments` (History)

Verify (once RBAC is in): a Remove/Sold with a contract drops the qty AND logs a row in History; below-zero is blocked; Sold ⇒ contract required; Other ⇒ note required. Table `stock_sale_adjustments` already created via manual DDL.

---

**2026-07-20 12:45 — you**

updates done to the page

---

**2026-07-20 12:45 — you**

Records: docs updated (actionAdjustSaleItem.md, actionViewSaleItemAdjustments.md, stock-for-sale.md); tech-session none-needed; status-note none-needed.
