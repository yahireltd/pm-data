---
id: T-0624
title: Sale-stock adjustments report — filterable list of all manual adjustments (reason, date, item, user)
type: feature
state: review
created: 2026-07-20T12:34:48Z
updated: 2026-07-21T05:33:18Z
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
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260720-1257
    model: claude-opus-4-8
    started: 2026-07-20T12:57:53Z
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-20T12:57:53Z
    ended: 2026-07-20T13:06:15Z
    summary: Added a Sale-Stock Adjustments report — a single page listing every manual add/remove made to sale stock, filterable by date range, reason (Sold/Binned/Other), item and user, with running totals (added / removed / net change). It's reached from a new "View all adjustments" button on the Stock For Sale screen. Previously adjustments could only be seen one item at a time via the per-item history pop-up; now managers can review all adjustments across items — e.g. everything binned this week, or every change made by a given person — which is the oversight the audit log was created for. Reuses the existing adjustments table, so no data changes are needed.
    test_plan: |-
      Prereqs (ops): grant the new route `/stock/sale-stock-adjustments` — add it to **Sales Permissions** if Sales should see the report (the `/stock/*` roles already cover it); then **commit** the working-tree code + docs. No migration — it uses the existing `stock_sale_adjustments` table.

      1. Stock For Sale → click **"View all adjustments"** (top-right) → the report opens.
      2. It lists all adjustments: When / Item / Change (signed, green +/red −) / Before→After / Reason / Note / Contract / User, newest first, with totals tiles (Adjustments / Added / Removed / Net Change).
      3. **Date range** From/To → Apply → list + totals update to the range.
      4. **Reason** (Sold/Binned/Other), **Item**, **User** filters → Apply → results scoped; selections persist in the form.
      5. **Clear** → all filters reset, full list returns.
      6. DataTable **search box** + **column sort** work on the filtered set.
      7. Edge: a filter with no matches → "No adjustments match the current filter."
      8. Cross-impact: the sale-stock page header now has two buttons (View all adjustments + Add / Transfer Stock) — confirm both still work and the Add/Transfer modal still opens.
      9. Access: a Sales user (non-superadmin) can open the report only once the route is granted; before granting, expect a 403.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
labels:
  - sale-stock
  - stock
  - reporting
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-20T13:06:15Z
version: 6
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

## Conversation

**2026-07-20 13:06 claude-code:** Run run-20260720-1257 completed — Added a Sale-Stock Adjustments report — a single page listing every manual add/remove made to sale stock, filterable by date range, reason (Sold/Binned/Other), item and user, with running totals (added / removed / net change). It's reached from a new "View all adjustments" button on the Stock For Sale screen. Previously adjustments could only be seen one item at a time via the per-item history pop-up; now managers can review all adjustments across items — e.g. everything binned this week, or every change made by a given person — which is the oversight the audit log was created for. Reuses the existing adjustments table, so no data changes are needed.

**2026-07-21 05:33 claude-code:** **Folded in: Item filter → log-scoped autocomplete (scales to 100s of items).**

The Item filter is now a **search-as-you-type** control instead of a `<select>`:
- New endpoint `actionSearchAdjustedItems($term)` (route `stock/search-adjusted-items`) returns only products that **have** adjustments matching the term (capped 20) — so the typeahead never surfaces zero-history items (unlike `ajax/all-items-search`).
- View: jQuery-UI autocomplete on `#itemSearch` → writes the picked id into hidden `#stockID`; pre-filled from `selectedItemName` after reload; clearing the box resets to "all items".
- **User stays a plain select** (small set).

**Extra RBAC route** to grant (in addition to `stock/sale-stock-adjustments`): **`stock/search-adjusted-items`** — `/stock/*` roles already cover it; add to **Sales Permissions** if Sales use the report.

Docs updated (action doc + new `actionSearchAdjustedItems.md` + view doc). Lint-clean, working tree (no-commit policy). Ticket stays in review.
