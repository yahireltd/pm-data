---
id: T-0637
title: "Quality checks: up to 3 photos per check (thumbnails + lightbox)"
type: feature
state: triaged
created: 2026-07-22T06:40:16Z
updated: 2026-07-22T07:05:00Z
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
version: 3
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

## Conversation

**2026-07-22 07:05 claude-code:** **Built (working tree, not committed).** Up to 3 photos per check.

**New / changed**
- Table `stock_quality_check_photos` + model `StockQualityCheckPhoto` (`MAX_PER_CHECK=3`) + `StockQualityCheck::getPhotos()` relation. DDL + migration of the existing single `photoUrl` in `sql/stock_quality_check_photos.sql` (run once). Old `photoUrl` column kept (dormant; still holds the first photo on new checks for back-compat).
- **Add Check modal**: photo input now `multiple` — "Photo Evidence (1–3, at least one)". JS `saveQualityCheck` validates 1–3 files (JPG/PNG/GIF/WEBP, ≤15MB each) and posts `qualityPhotoFile[]`.
- `actionSaveQualityCheck`: requires ≥1, caps at 3, uploads each (`stock-quality-checks` bucket), saves the check + a photo row per file.
- `renderQualityCheckCard`: `[ score ][ notes + who·date ][ up to 3 thumbnails → ]`; thumbnails (50×50) open the shared lightbox (`openFailurePointImage`). "View Photo Evidence" link removed. Falls back to legacy `photoUrl` pre-migration.
- Docs: feature doc.

**RBAC:** unchanged — same `quality` edit gate as before (`save-quality-check`).

**Test plan (human review):**
1. Run `sql/stock_quality_check_photos.sql`. Existing checks show their photo as the first thumbnail.
2. Add Check → attach **1 photo** → saves; card shows one 50×50 thumbnail on the right; click → lightbox.
3. Add Check → attach **3 photos** → all three show as thumbnails.
4. **0 photos** blocked (client + server); **>3** blocked (client + server).
5. Oversize/invalid file rejected with a message.
6. Non-quality-edit user: can't add checks (403), thumbnails read-only.
7. Cross-impact: scoring/tiles, grade cards, failure points, notes all unchanged.
