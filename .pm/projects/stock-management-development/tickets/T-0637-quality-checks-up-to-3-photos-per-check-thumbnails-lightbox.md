---
id: T-0637
title: "Quality checks: up to 3 photos per check (thumbnails + lightbox)"
type: feature
state: review
created: 2026-07-22T06:40:16Z
updated: 2026-07-22T07:31:56Z
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
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260722-0731
    started: 2026-07-22T07:31:33Z
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-22T07:31:33Z
    ended: 2026-07-22T07:31:56Z
    summary: Quality checks can now carry up to three photos each instead of just one. Staff attach 1–3 photos when logging a check, and they appear as thumbnails on the check card; clicking a thumbnail opens it full-size like the other photos on the page. Existing single photos were carried over with no loss. This gives a fuller visual record of each quality assessment.
    test_plan: |-
      1. Run the migration (sql/stock_quality_check_photos.sql). Existing checks show their photo as the first thumbnail.
      2. Add Check with 1 photo → saves; one thumbnail on the right; click → opens the shared lightbox.
      3. Add Check with 3 photos → all three show as thumbnails (fixed 3-wide area).
      4. Try 0 photos → blocked (client + server); try 4 photos → blocked (client + server).
      5. Oversize (>15MB) or non-image file → rejected with a message.
      6. Non-quality-edit user: cannot add checks (403); thumbnails read-only.
      7. Cross-impact (shared Quality section): scoring tiles, grade cards, failure points, and overall notes all still work.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: docs/features/stock-quality-check-photos.md
labels:
  - quality-management
  - stock
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-22T07:31:56Z
version: 6
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

**2026-07-22 07:31 claude-code:** Run run-20260722-0731 completed — Quality checks can now carry up to three photos each instead of just one. Staff attach 1–3 photos when logging a check, and they appear as thumbnails on the check card; clicking a thumbnail opens it full-size like the other photos on the page. Existing single photos were carried over with no loss. This gives a fuller visual record of each quality assessment.
