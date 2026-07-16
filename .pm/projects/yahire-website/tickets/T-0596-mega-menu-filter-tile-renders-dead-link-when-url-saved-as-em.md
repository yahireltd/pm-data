---
id: T-0596
title: Mega-menu filter tile renders dead link when url saved as empty string (not NULL)
type: bug
state: done
created: 2026-07-16T06:20:17Z
updated: 2026-07-16T06:49:35Z
project: yahire-website
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  contact: zsolt@yahire.com
assignee: null
acceptance_criteria:
  - "[x] Saving a filter image with a blank URL field in Site Manager stores NULL in ya_filter_images.url, not an empty string (both add and update actions)"
  - "[x] A mega-menu filter tile with an empty/whitespace/'#' url falls back to the category link (e.g. /table-hire) instead of an empty href"
  - "[x] Tall & Poseur tile in the Table Hire mega-menu links correctly: hover shows /table-hire and click navigates/filters rather than reloading"
  - "[x] Existing tiles that navigate via filterOn=1 + filters continue to work unchanged"
out_of_scope: []
code_anchors:
  - path: yahirenew/views/layouts/main.php
    line: 484
    note: Config URL resolution — now treats '' / whitespace as unset and falls back to category link
  - path: yahirenew/views/layouts/main.php
    line: 638
    note: Final href guard — also catches '' and '#'
  - path: backend/controllers/SiteManagementController.php
    line: 600
    note: actionAddFilterImage — normalise blank url to NULL
  - path: backend/controllers/SiteManagementController.php
    line: 699
    note: actionUpdateFilterImage — normalise blank url to NULL
  - path: yahirenew/web/js/origin.js
    line: 626
    note: handleCtaTileClick — POSTs to href action; empty action caused the page reload
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - frontend
  - backend
  - mega-menu
  - filter-images
attention: null
version: 10
defect_status: confirmed
---

## Problem

The "Tall & Poseur" CTA tile in the Table Hire mega-menu had a dead link: hovering showed only the base site URL (no `/table-hire`), and clicking just reloaded the current page. The image itself displayed fine (valid 537 KB webp on S3, HTTP 200), so the fault was the link, not the image.

## Root cause

A colleague edited that tile in Site Manager. The admin save wrote an **empty string** (`''`) to `ya_filter_images.url`, whereas its untouched siblings (Dining & Banquet, Coffee & Side) had `url = NULL`.

- **Backend save**: `actionAddFilterImage` / `actionUpdateFilterImage` read `$url = $postData['url'] ?? null`. The form always submits the `url` field, so a blank input arrives as `''` (not absent) — `?? null` doesn't catch it, and `''` is written to the DB. (Note `sort_order` and `filterOn` are already normalised for `'' || null`, but `url` was not.)
- **Frontend render**: `main.php` used `$visual['url'] ?? '/table-hire'`. The `??` (null-coalescing) fallback only fires on `NULL`, so `''` passed straight through → empty `href`. Because the tile still has `filterOn=1`, the JS handler (`origin.js` `handleCtaTileClick`) POSTs to an empty action = the current page → reload.

## Data anomaly (verified)

```
id=7 Dining & Banquet  url=NULL         filterOn=1   -> works
id=8 Tall & Poseur     url='' (len 0)   filterOn=1   -> broken
id=9 Coffee & Side     url=NULL         filterOn=1   -> works
```

## Fix applied (working tree, not yet committed/deployed)

1. **Data** (already done by reporter): reset `ya_filter_images.url` to `NULL` for id 8 — tile working again.
2. **Backend** `SiteManagementController.php`: normalise blank/whitespace `url` to `NULL` before save, in both add and update actions.
3. **Frontend** `views/layouts/main.php`: treat `''`/whitespace (and `'#'`) as "not set" so the tile falls back to the category link instead of an empty href — at both the config-resolution point and the final href guard.

## Deployment note

Two separate apps must both ship: the `backend` admin (Site Manager) and the `yahirenew` frontend.

## Follow-up (optional)

The `group` field in the same two save actions has the identical `?? null` pattern and could also store `''`. It only affects tile grouping display, not links, so lower priority — worth normalising for consistency.

## Conversation

**2026-07-16 06:22 claude-code:** **Shipped by Zsolt — live and committed.**

Commit: `64312db` "Ignore blank menu URLs". All three fix layers are in the commit and deployed to production:
- Backend `SiteManagementController.php` — blank/whitespace `url` normalised to `NULL` on save (add + update filter-image actions).
- Frontend `views/layouts/main.php` — empty/whitespace/`#` url treated as unset, falls back to the category link (both the config-resolution point and the final href guard).
- Data: `ya_filter_images.url` for the Tall & Poseur row (id 8) reset from `''` to `NULL`.

**Verification checklist for close:**
1. On the live site, open the Table Hire mega-menu → hover **Tall & Poseur**: status bar shows `/table-hire` (not just the base domain), and clicking navigates/filters rather than reloading the current page.
2. Confirm its siblings (Dining & Banquet, Coffee & Side) still work as before.
3. In Site Manager, edit a filter image leaving the URL field blank and save → check `ya_filter_images.url` stores `NULL`, not `''` (test both a new tile and an edit of an existing one).
4. Spot-check another category's mega-menu to confirm no regression in tiles that use a real configured `url`.

Not yet done (optional follow-up, noted in body): the `group` field has the same `?? null` pattern and can still store `''` — cosmetic only.

---

**2026-07-16 06:22 — you**

fixed the issue, tested

---

**2026-07-16 06:22 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
