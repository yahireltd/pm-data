---
id: T-0555
title: "Sale stock: manual add/remove quantity with a reason note (Sold / Binned / contract #) + audit log"
type: feature
state: ready
created: 2026-07-14T05:32:53Z
updated: 2026-07-14T05:32:57Z
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
assignee: null
acceptance_criteria:
  - On the sale-stock screen, a staff member can manually adjust (add OR remove) an item's sale-stock quantity.
  - Each manual adjustment requires a reason note (e.g. Sold / Binned) and can optionally include a contract number for the record.
  - "Every adjustment is logged: who, when, +/- amount, reason note, contract number — an audit trail viewable against the item."
  - "Existing automatic behaviour is unchanged: quote→contract conversion still deducts the sale-stock qty, and re-adding the item still tops the number up."
  - Validation prevents nonsensical results (e.g. no silent negative stock without an explicit reason).
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
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sale-stock
  - stock
attention: null
version: 2
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