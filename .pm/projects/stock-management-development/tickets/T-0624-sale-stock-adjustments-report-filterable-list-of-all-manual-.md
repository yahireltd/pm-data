---
id: T-0624
title: Sale-stock adjustments report — filterable list of all manual adjustments (reason, date, item, user)
type: feature
state: triaged
created: 2026-07-20T12:34:48Z
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
  channel: follow-up from T-0555
assignee: null
acceptance_criteria:
  - "A dedicated report page lists all stock_sale_adjustments rows with: date/time, product/item, signed +/- delta, before -> after, reason, note, contract #, and user."
  - Filterable by reason (Sold/Binned/Other), date range, item/product, and user; free-text search across the visible columns.
  - "Shows summary totals for the current filter (at least: number of adjustments and net qty change)."
  - A 'View all adjustments' link/button on the Stock For Sale screen opens the report.
  - Access is gated by RBAC on the same basis as the sale-stock screen (new route granted; Sales Permissions added if Sales should have it).
  - "Out of scope: a unified cross-system audit hub (merges/price-changes/stock-transactions) — that's a separate spike."
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: add actionSaleStockAdjustments (report) near the sale-stock actions (~L7235 actionStockForSale); query StockSaleAdjustment with filters
  - path: ya-hire/common/models/StockSaleAdjustment.php
    note: audit model to query — relations getSaleItem/getProduct/getUser already defined (T-0555)
  - path: ya-hire/backend/views/stock/stock-for-sale.php
    note: add the 'View all adjustments' link (near the Add/Transfer header button)
  - path: ya-hire/backend/views/stock/
    note: NEW view sale-stock-adjustments.php — report page (DataTable + reason/date/item/user filters + totals); match the sfs-page facelift style
relates:
  - T-0555
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sale-stock
  - stock
  - reporting
attention: null
version: 2
---

## Problem
T-0555 added manual sale-stock adjustments with an audit log (`stock_sale_adjustments`), but the trail is only viewable **per item** via the History modal on the sale-stock screen. There's no way to see adjustments **across** items — e.g. "all Binned items this week", "everything adjusted by Sandor", or "all removals against contract X".

## What's wanted
A dedicated **Sale-Stock Adjustments** report page listing every adjustment, filterable/searchable, linked from the Stock For Sale screen.

## Context
- Source: follow-on ("Tier 1") from the T-0555 discussion. Tier 2 (a unified cross-system audit hub over merges / price changes / stock transactions / adjustments) is deliberately **out of scope** here — different schemas, its own spike.
- The audit rows already exist and carry everything needed: `delta` (signed), `qtyBefore`/`qtyAfter`, `reason`, `note`, `contractNo`, `userID`, `createdAt`, plus `saleItemID`/`stockID` for the item/product.
- Mirror the existing filtered-report patterns on this project (e.g. the price-changes viewer, `stock-for-sale` DataTable).

## RBAC
New report route must be granted like T-0555's actions — the `/stock/*` roles are auto-covered; **Sales Permissions** needs the new route added explicitly if Sales should see it.