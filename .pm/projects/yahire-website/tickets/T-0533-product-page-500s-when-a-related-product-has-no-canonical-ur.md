---
id: T-0533
title: Product page 500s when a related product has no canonical URL
type: bug
state: triaged
created: 2026-07-09T09:14:03Z
updated: 2026-07-09T09:15:53Z
project: yahire-website
section: null
parent: null
children: []
order: 11264
priority: p1
reporter:
  kind: human
  name: Zsolt
  contact: zsolt@yahire.com
assignee: null
acceptance_criteria:
  - Adding a related product that has no canonical URL for the target website is rejected in actionAddRelatedItem with a clear error and no row is written to ya_related_items
  - Adding a related product that DOES have a canonical URL still succeeds and renders normally
  - The broken 'No canonical set' recolour logic (testing the always-empty $checkItem) is removed
  - Existing ya_related_items rows with no canonical URL are identified via the detection query and cleaned up (or the ChlYii view is hardened to skip them without 500-ing)
  - Confirmed which deployment/admin writes the live CHL related-items table so the guard actually protects production
out_of_scope: []
code_anchors:
  - file: backend/controllers/SiteManagementController.php
    symbol: actionAddRelatedItem
    note: "Guard added: block save when related product has no canonical URL"
  - file: chl/views/chl/product.php
    line: 526
    note: Front-end line that reads $relatedItem['url'] and 500s when the key is absent
  - file: common/models/YaRelatedItems.php
    symbol: getProductCanonicalUrl
    note: "Canonical URL resolution: ya_url_routes stockID=relatedID, canonicalURL=1, websiteID"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - website
  - chl
  - related-items
attention: null
version: 2
---

## Problem
The CHL (websiteID 2) product page returns a **500** for some products. Live error:

```
yii\base\ErrorException: Undefined index: url
in /var/www/html/ChlYii/chl/views/chl/product.php:526
```

Yii escalates the PHP notice to a fatal exception, so a single bad entry takes down the whole product page. Observed on the Teak Garden Chair page (stockID 628); Zsolt restored it by manually removing the offending related item.

## Root cause
`product.php:526` loops the page's **related items** and reads `$relatedItem['url']` (the related product's canonical `urlFrom`). If a related product has **no canonical URL** (`ya_url_routes.canonicalURL = 1`) for that website, the front-end build has no `url` key for that item → `Undefined index: url` → 500.

The related item gets into that state in the backend: `SiteManagementController::actionAddRelatedItem()` calls `$newRelatedItem->save(false)` **unconditionally**, with no check that the related product has a canonical URL. The pre-existing "No canonical set" indicator was also broken — it tested `$checkItem['productCanonicalUrl']`, which is always empty in the add branch (so it fired for every add), and it only recoloured the box rather than preventing the save.

## Fix applied (working tree, yasite repo — not yet committed)
`backend/controllers/SiteManagementController.php` `actionAddRelatedItem()`:
- Before saving, verify the related product has a `canonicalURL = 1` route for the posted `websiteID`; if not, return an error (`Cannot add "<name>" as a related item: it has no canonical URL set for this website.`) and **do not save**.
- Removed the broken "No canonical set" recolour block (dead once the guard is in place).

## Still outstanding
1. **Existing bad rows** already in `ya_related_items` still break pages one at a time. Detection query (returns offenders, 0 = clean):
```sql
SELECT ri.id AS related_item_id, ri.websiteID, ri.mainID AS main_product_id,
       ri.relatedID AS related_product_id, p.productName AS related_product_name
FROM ya_related_items ri
LEFT JOIN ya_url_routes ur
       ON ur.stockID = ri.relatedID AND ur.websiteID = ri.websiteID AND ur.canonicalURL = 1
LEFT JOIN ya_products p ON p.id = ri.relatedID
WHERE ur.id IS NULL
ORDER BY ri.websiteID, ri.mainID;
```
2. **Deployment mismatch** — the live 500 is thrown from the separate `/var/www/html/ChlYii` deployment, while the fix lands in the `yasite` backend. Confirm this backend is what writes the related-items table for the live CHL site (shared admin), and decide whether the ChlYii front-end view should also guard `$relatedItem['url']` defensively so a bad row degrades gracefully instead of 500-ing.

## Context
- CHL = websiteID 2 (`chl/config/params.php`, `ChlController::$websiteID`).
- Canonical resolution: `YaRelatedItems::getProductCanonicalUrl()` → `ya_url_routes` where `stockID = relatedID AND canonicalURL = 1 AND websiteID`.