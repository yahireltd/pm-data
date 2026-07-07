---
id: T-0519
title: "Get-inspired page: bundle price showed £0 and prices missing the £ sign"
type: bug
state: done
created: 2026-07-07T10:40:52Z
updated: 2026-07-07T10:45:33Z
project: yahire-website
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Thanos
assignee: null
acceptance_criteria:
  - "[x] Bundle/package products on the get-inspired page show their calculated price (sum of component unitPrice × qty) instead of £0."
  - "[x] All get-inspired product cards render the price with a leading £ and 2 decimals (e.g. 'From £187.50')."
  - "[x] Normal (non-bundle) products are unchanged apart from the £ + formatting."
  - '[x] The structured-data <meta itemprop="price"> still outputs the numeric value (£ stripped).'
out_of_scope: []
code_anchors:
  - path: common/models/YahireNewSections.php
    note: "getProductCardFromStockID() (~line 2453): bundle price fallback via getBundleCalculatedPrice() + '£' + number_format(,2) on both price paths"
  - path: common/models/YaProducts.php
    note: "getBundleCalculatedPrice() (~line 137): sums packageItems unitPrice × qty for isPackage/isMultiItemBundle products"
  - path: yahirenew/controllers/YahireController.php
    note: "actionGetInspired(): the stock-ID arrays (black/ice/heritage/gold/urban-industrial selections, ~line 5480-5541) were updated to change which products display on the page"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - get-inspired
  - pricing
  - yahirenew
attention: null
version: 9
defect_status: confirmed
---

## Problem
On the **get-inspired** page (yahirenew), product cards had two pricing issues:
1. **Bundle/package products displayed £0** instead of a price.
2. Prices were rendered **without the `£` sign** (bare number).

## Root cause
Both come from `YahireNewSections::getProductCardFromStockID()` (the shared method that builds these cards):
- It read the price **only** from the product's `discountMatrix`. Bundles/packages have no discount-matrix rows, so `$price` was never set → the card fell back to `0`.
- It passed the **raw price number** to `productCard()` with no `£` and no formatting. The card template prints `$price` as-is (it expects the value to already include the `£` — hence the existing `ltrim($price, '£')`), so cards showed a bare number.

## Fix
In `getProductCardFromStockID()`:
- Initialise `$price`/`$priceText` (removes an undefined-variable warning).
- **Bundle fallback:** when there's no matrix price and the product is a package / multi-item bundle, use the existing `YaProducts::getBundleCalculatedPrice()` helper (sum of component `unitPrice × qty`) shown as "From £…".
- **Format both** normal and bundle prices as `£` + 2 decimals: `'£' . number_format($value, 2)`.

## Notes
- This method is shared, so the fix also corrects pricing on the other cards built by it (the hoxton/bodrum/skandi/lisbon/scaffold "table" cards).
- Caveat: a bundle's price comes from its `packageItems` (`unitPrice × qty`); if a bundle still shows £0, that package's items have no unit prices (a data issue, not code).
- Committed to `master` and deployed to live.

## Conversation

**2026-07-07 10:43 claude-code:** **Also part of this change (for the trace):** the **product selection** on the get-inspired page was updated — the stock-ID arrays in `YahireController::actionGetInspired()` (the black / ice / heritage / gold / urban-industrial selections, ~lines 5480-5541) were changed to swap which products appear. This is what surfaced the bundle-pricing bug (a newly-added bundle showed £0). Committed to `master` and live alongside the price fix.

---

**2026-07-07 10:45 — you**

done and live

---

**2026-07-07 10:45 — you**

Records: docs updated (getBundleCalculatedPrice.md); tech-session none-needed; status-note none-needed.
