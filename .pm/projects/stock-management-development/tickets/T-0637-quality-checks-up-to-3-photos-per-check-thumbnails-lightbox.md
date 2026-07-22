---
id: T-0637
title: "Quality checks: up to 3 photos per check (thumbnails + lightbox)"
type: feature
state: triaged
created: 2026-07-22T06:40:16Z
updated: 2026-07-22T06:40:26Z
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
  channel: follow-up from T-0553
assignee: null
acceptance_criteria:
  - A quality check can have up to 3 photos (minimum 1 still required, max 3), uploaded in the Add Check modal.
  - Check cards show the check's photos as small thumbnails on the right (replacing the 'View Photo Evidence' link).
  - Clicking a thumbnail opens the shared image lightbox (same as the rest of the Quality page).
  - Existing single-photo checks are preserved / migrated (the photo shows as the first thumbnail).
  - Add-time only — no editing photos on existing checks.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: actionSaveQualityCheck (~L20335) single photo upload → up to 3; renderQualityCheckCard (score tile + details + View Photo Evidence link) → thumbnails
  - path: ya-hire/common/models/StockQualityCheck.php
    note: single photoUrl field (kept dormant)
  - path: ya-hire/common/models
    note: NEW StockQualityCheckPhoto model + stock_quality_check_photos table (checkID, photoUrl, createdAt)
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: Add Check modal photo input + saveQualityCheck JS
relates:
  - T-0553
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quality-management
  - stock
attention: null
version: 2
---

## Source
Follow-up from **T-0553**. That ticket's original "up to 3 photos (currently 1)" was delivered as the 4 reference **grade cards**; the **per-check** photos were left as-is. This ticket covers the per-check photos.

## Problem
A Quality Check stores a **single** photo (`StockQualityCheck.photoUrl`, required) and the card shows a **"View Photo Evidence"** text link.

## What's wanted
- **Up to 3 photos per check** (min 1 required, max 3), uploaded **when logging the check**.
- Show them as **small thumbnails on the right of the check card**, replacing the text link.
- Click a thumbnail → the **shared image lightbox** used elsewhere on the Quality page (`openFailurePointImage`).

## Notes / decisions (with Zsolt)
- **Storage:** new child table `stock_quality_check_photos` (one row per photo), consistent with the grade-photos pattern. Migrate the existing single `photoUrl` in as the first photo; keep the old column dormant.
- **Add-time only** — no editing photos on existing checks.
- Assets stay in the `stock-quality-checks` bucket.