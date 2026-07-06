---
id: T-0515
title: "Bundle editor: package part qty double-scaled when editing bundle qty on a loaded contract"
type: bug
state: review
created: 2026-07-06T04:59:18Z
updated: 2026-07-06T04:59:50Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Sandor
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Editing a package/bundle item's qty on a saved/reloaded contract sets each package-definition part to partQty × packageQty (12 poseur tables → 24 frames, not 18).
  - The corrected value persists on save and is what picking reads from ya_contract_items.
  - User-added (non-package) accessories on a multipart item still scale proportionally when the parent qty changes.
  - The fresh-add path (adding a bundle in-session) is unchanged and still correct.
  - The fix does not rely on the isPackageChild flag (works whether the contract is fresh or DB-loaded).
  - SalesController.php passes php -l.
out_of_scope: []
code_anchors:
  - path: backend/controllers/SalesController.php
    note: "actionUpdateItemQty: added $handledPackageChildStockIDs tracker (~3833); Loop A records each package child (~3863); Loop B skips tracked children (~3933) before the buggy ratio rescale (~3919 newAccQty = round(existingAccQty * qty / originalParentQty))"
  - path: common/models/QuoteItems.php
    note: isPackageChild flag set only on fresh add (line 1768); absent on DB-loaded contract accessories — why the old guard failed
  - path: common/models/YaProductsPackegeItems.php
    note: "package part definition: qty column = parts per 1 package (partQty)"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260706-0459
    model: claude-opus-4-8
    started: 2026-07-06T04:59:27Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-06T04:59:27Z
    ended: 2026-07-06T04:59:50Z
    summary: "Fixed a bug in the bundle editor on the quote-builder where changing the quantity of a multi-part product (a \"bundle\", e.g. a poseur table made of a top plus 2 frames) on an existing contract silently produced the wrong number of parts. The system calculated the parts correctly once, then a second piece of code re-scaled them again, so 12 tables gave 18 frames instead of 24. The warehouse then picked and loaded the wrong count — which is exactly what happened on the flagged contract and prompted the email. Cause: the two calculation steps were meant to be mutually exclusive, but the safeguard that kept them apart only worked while a bundle was being added fresh in the same session; once a contract was saved and reopened, the safeguard was missing, so both steps ran and the parts were scaled twice. The fix makes the second step reliably skip anything the first step already handled, so bundle parts are always exactly parts-per-unit × quantity. Without this, any edit to a bundle's quantity on a saved contract quietly corrupted its part counts (the error scaled with how much the quantity changed), risking wrong loads on the day. Note: the code only recalculates when a bundle qty is next edited, so already-affected contracts (including the flagged one) still hold wrong numbers until re-saved or corrected in the database — an audit query to find them is included on the ticket. Change is written and syntax-checked but not yet committed/deployed."
    test_plan: |-
      Prereq: deploy the working-tree change (uncommitted on master). Test on the flagged contract C091789 and a fresh one.

      Core fix (edit on a loaded contract):
      1. Open C091789 in quote-builder. Change the Black Element Poseur Table qty to 10 → confirm frames update to 20 (2×10).
      2. Change it to 12 → confirm frames show 24 (NOT 18), line totals look right.
      3. Save, reopen, confirm 24 persists and the item breakdown/picking shows 24.
      4. Try another decrease/increase (e.g. 12→8→15) and confirm frames track exactly 2× each time.

      Regression — user-added accessories (must still scale proportionally):
      5. On a multipart item, add a user-selected accessory via the dropdown (one NOT part of the package definition). Change the parent qty and confirm that accessory still scales by the old:new ratio (this path is intentionally unchanged).

      Regression — fresh add:
      6. Add a bundle fresh in a new quote, set qty 12, confirm parts = partQty×12 immediately (unchanged behaviour).
      7. Add bundle, then edit its qty in the same session before saving → confirm parts still correct (the in-session flag path still works).

      Other bundle types:
      8. Repeat step 1-3 with a different package product (not poseur tables) to confirm the fix is generic.

      Data cleanup / audit:
      9. Run the audit query on the ticket to list contracts where a stored part qty ≠ partQty×parentQty. Spot-check a couple against the contract to confirm they're genuine (not intentional manual overrides).
      10. Correct C091789's stored frames to 24 (re-save via the fixed editor is cleanest; direct UPDATE as fallback).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - quote-builder
  - bundle-editor
  - packages
  - sales
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-06T04:59:50Z
version: 4
---

## Symptom
On contract C091789, a "Black Element Poseur Table" bundle (2 frames per table) was changed to **12 tables** but the breakdown + picking showed **18 frames** instead of **24**. Warehouse would have loaded the wrong count. Reported by Sandor (Stock Manager), escalated by Nathan/Austin.

## Root cause — `SalesController::actionUpdateItemQty()`
When a package parent's qty changes, the code ran **two** loops over the children:
- **Loop A** (correct): reads the package definition and sets each child = `partQty × packageQty` → frames = `2 × 12 = 24`.
- **Loop B** (meant only for user-added extras): re-scales children proportionally: `newQty = round(existingQty × newParentQty / oldParentQty)`. It read the `24` Loop A had just written and rescaled it AGAIN by `12/16` (pre-edit qty was 16) → `round(24 × 12 / 16) = 18`, overwriting the correct value and emitting a second JS update with the same `itemRef` (last-wins → UI shows 18; save persists 18 → picking reads 18).

Loop B was supposed to skip package children via the `isPackageChild` flag, but that flag is only set on a **fresh in-session add** (`QuoteItems.php:1768`); when a contract is **loaded from the DB** the accessories are rebuilt without it (`SalesController.php` load ~2215-2240), so the guard failed.

**Scope:** the error factor is exactly `newQty ÷ oldQty`, so ANY bundle-qty edit on a saved contract mis-scales its package parts. Not poseur-specific.

## Fix (applied, working tree)
In `actionUpdateItemQty`: Loop A now records the package-definition child stock ids it handled (`$handledPackageChildStockIDs`); Loop B skips any child in that set. Package parts are therefore computed once (`partQty × packageQty`); only genuine user-added accessories still get the proportional rescale. No longer depends on the unreliable `isPackageChild` flag.

## Data model (for reference / audit)
- Package def: `ya_products_package` (packageID, stockID=package product) → `ya_products_packege_items` (packageID, stockID=part, **qty = parts per package**).
- Contract items `ya_contract_items`: parent line has `isAccessory=0`; children have `isAccessory=1`, `parentID = parent.stockID`, `parentRef = parent.itemRef`.

## Audit query — find other contracts already corrupted
```sql
SELECT p.contractID, p.stockID AS packageStockID, p.itemName AS packageName, p.qty AS packageQty,
       c.stockID AS partStockID, c.itemName AS partName, c.qty AS storedPartQty,
       (CASE WHEN pki.qty > 0 THEN pki.qty ELSE 1 END) AS partQtyPerPackage,
       (CASE WHEN pki.qty > 0 THEN pki.qty ELSE 1 END) * p.qty AS expectedPartQty,
       c.qty - (CASE WHEN pki.qty > 0 THEN pki.qty ELSE 1 END) * p.qty AS diff
FROM ya_contract_items p
JOIN ya_contract_items c ON c.contractID = p.contractID AND c.parentRef = p.itemRef AND c.isAccessory = 1
JOIN ya_products_package pkg ON pkg.stockID = p.stockID
JOIN ya_products_packege_items pki ON pki.packageID = pkg.packageID AND pki.stockID = c.stockID
WHERE p.isAccessory = 0
  AND c.qty <> (CASE WHEN pki.qty > 0 THEN pki.qty ELSE 1 END) * p.qty
ORDER BY p.contractID, p.stockID, c.stockID;
```
Optional: join `ya_contracts` on `id = p.contractID` and filter `hireStartDate >= CURDATE() AND unconverted = 0` to focus on live/upcoming jobs.

## Notes
- Code fix only recomputes on the next edit — C091789's stored 18 needs correcting (re-save via the fixed editor, or direct `UPDATE ya_contract_items`).
- Yasystem protected `master`; uncommitted working-tree change pending test + deploy.

## Conversation

**2026-07-06 04:59 claude-code:** Run run-20260706-0459 completed — Fixed a bug in the bundle editor on the quote-builder where changing the quantity of a multi-part product (a "bundle", e.g. a poseur table made of a top plus 2 frames) on an existing contract silently produced the wrong number of parts. The system calculated the parts correctly once, then a second piece of code re-scaled them again, so 12 tables gave 18 frames instead of 24. The warehouse then picked and loaded the wrong count — which is exactly what happened on the flagged contract and prompted the email. Cause: the two calculation steps were meant to be mutually exclusive, but the safeguard that kept them apart only worked while a bundle was being added fresh in the same session; once a contract was saved and reopened, the safeguard was missing, so both steps ran and the parts were scaled twice. The fix makes the second step reliably skip anything the first step already handled, so bundle parts are always exactly parts-per-unit × quantity. Without this, any edit to a bundle's quantity on a saved contract quietly corrupted its part counts (the error scaled with how much the quantity changed), risking wrong loads on the day. Note: the code only recalculates when a bundle qty is next edited, so already-affected contracts (including the flagged one) still hold wrong numbers until re-saved or corrected in the database — an audit query to find them is included on the ticket. Change is written and syntax-checked but not yet committed/deployed.
