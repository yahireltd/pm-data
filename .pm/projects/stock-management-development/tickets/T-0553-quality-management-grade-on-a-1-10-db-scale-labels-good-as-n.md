---
id: T-0553
title: "Quality Management: grade on a 1–10 DB scale (labels Good as new/Good/OK/Needs replaced) + up to 3 photos per grade"
type: feature
state: done
created: 2026-07-14T05:07:50Z
updated: 2026-07-22T08:32:48Z
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
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - "[x] The Quality section's 3 fixed reference-image slots (Good / Average / Bad) are replaced by 4 quality cards, one per label, each header showing the label + its score range: Good as new (8–10), Good (6–7), OK (4–5), Needs replaced (1–3)."
  - "[x] The existing 1–10 scoring stays as-is (summary tiles, check cards, Add Check modal all unchanged); the 4 labels map onto fixed ranges of the 1–10 scale (1–3 / 4–5 / 6–7 / 8–10), surfaced in the card headers."
  - "[x] Each quality card supports up to 4 reference photos shown in a 2×2 grid, with add + remove per photo (currently a single photo per slot)."
  - "[x] Existing reference images are migrated onto the new cards (Bad → Needs replaced, Average → OK, Good → Good), no data loss."
  - "[x] Layout keeps the same row structure — the Failure Points / Other Notes column plus the 4 quality cards (5 equal-width columns); Other Notes stays unchanged (one-to-many notes split to T-0633)."
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: buildQualitySectionContent() (~L15181) + renderSection('quality') (~L12698) — quality tab render + edit modal
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: product info page — Quality section UI (grade display + photos)
  - path: ya-hire/common/models/StockQualityInfo.php
    note: quality grade + photo storage — where the 1–10 scale and multi-photo set live
  - path: ya-hire/common/models/StockQualityCheck.php
    note: per-check quality record (dated grade entries)
relates:
  - T-0633
  - T-0637
  - T-0639
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quality-management
  - stock
attention: null
version: 17
---

## Source
From the kickoff meeting (M-001) — Sandor's + Ben's stock documents. First quick-win in Milestone MS-001.

## Problem
Quality grading today is fixed **good / average / bad** with **one photo each**, and there's no consistent, future-proof numeric scale — so we can't get more granular later without reworking/losing past inputs.

## What's wanted
- **Simpler labels now:** Good as new / Good / OK / Needs replaced.
- **Store on a 1–10 DB scale** so the labels sit on a reserved numeric range — we can expand granularity in future without losing the completed scoring (Ben's "align labels with a 1–10 DB scale" idea; kept simple for now per the "simpler is better for now" steer).
- **Up to 3 photos per grade** (currently 1).

## Open questions (confirm on pickup)
1. Exact mapping of the 4 labels onto the 1–10 points (e.g. 10 / 7 / 4 / 1?).
2. Photo storage: extend the existing single-photo field to a set of up to 3 — confirm storage location/format matches how other stock photos are handled.
3. Does the grade live on `StockQualityInfo` (current state) or per-check on `StockQualityCheck` (history), or both?

## Out of scope (separate tickets)
- Failure points rework, the all-stock quality overview page, and quality-over-time views — those are their own tickets in this milestone.

## Conversation

**2026-07-21 11:47 claude-code:** **Design finalised with Zsolt — resolves the open questions. Building now.**

Decisions:
- **Keep the 1–10 scoring exactly as-is** (summary tiles, check cards "X/10", Add Check modal). No change to scoring/capture.
- The **3 reference-image slots (Good/Average/Bad) become 4 grade cards**, one per label, header = **label + score range**: Good as new (8–10) · Good (6–7) · OK (4–5) · Needs replaced (1–3).
- **Each card holds up to 4 photos in a 2×2 grid** (add up to 4, remove per photo).
- **Layout unchanged** — same row, now **5 equal-width columns**: Failure Points/Other Notes column + the 4 grade cards.
- **Other Notes stays a single box** here; the one-to-many notes rework is split to **T-0633** (same sprint).

Plan:
1. New child table `stock_quality_grade_photos` (`stockID, grade [good_as_new|good|ok|needs_replaced], photoUrl, createdBy, createdAt`) + model `StockQualityGradePhoto` (manual DDL). Keep the 3 legacy `*QualityImageUrl` columns as a dormant safety-net; migrate their values in (**Bad→needs_replaced, Average→ok, Good→good**; Good as new starts empty).
2. Rework `buildQualitySectionContent` image section → loop the 4 grades; each card = "Label (range)" + 2×2 photo grid + Add Image tile (while <4) + per-photo remove.
3. `actionUploadGradePhoto` (cap 4/grade) + `actionDeleteGradePhoto`; same `quality` edit permission gate + `stock-quality-info` asset bucket as today.
4. JS wiring; grade photos reuse the existing click-to-enlarge lightbox.
5. RBAC (`stock/*`) + docs.

**2026-07-21 11:53 claude-code:** **Built (working tree, not committed).** 3 reference-image slots → 4 grade cards with up to 4 photos each.

**New / changed**
- Table `stock_quality_grade_photos` + model `StockQualityGradePhoto` — DDL **and** legacy-image migration in `sql/stock_quality_grade_photos.sql` (run once). Grades in `StockQualityGradePhoto::grades()`: Good as new (8–10) / Good (6–7) / OK (4–5) / Needs replaced (1–3). Cap 4/grade.
- `buildQualitySectionContent` — the good/average/bad loop is replaced by 4 grade cards, each a **2×2 photo grid** + per-photo **×** remove + **Add** tile (while <4). Row stays 5 equal-width columns (Failure Points/Other Notes + 4 grade cards). Photos reuse the existing lightbox.
- `actionUploadGradePhoto` (caps at 4) + `actionDeleteGradePhoto`; same `quality` edit gate + `stock-quality-info` bucket. JS `uploadGradePhoto`/`deleteGradePhoto` in `view-product-info.php`.
- **Scoring untouched** (tiles, check cards, Add Check). Old `*QualityImageUrl` columns kept (dormant); values migrated in (Bad→needs_replaced, Average→ok, Good→good). Legacy `actionUpload/DeleteQualityImage` now dormant.
- Docs: feature doc + 2 action docs.

**RBAC:** `stock/upload-grade-photo`, `stock/delete-grade-photo` — covered by `stock/*` (management access) + the quality-edit perm.

**Test plan (human review):**
1. Run `sql/stock_quality_grade_photos.sql`. Products that had good/avg/bad images show them as the first photo in **Good / OK / Needs replaced** cards respectively; **Good as new** empty.
2. Quality tab shows **4 grade cards** with headers **Good as new (8–10) / Good (6–7) / OK (4–5) / Needs replaced (1–3)**, in 5 equal columns.
3. **Add** up to 4 photos to a card (2×2). At 4, the Add tile disappears; a 5th upload is blocked server-side.
4. **×** on a photo (confirm) removes it + its asset; the Add tile reappears.
5. Click any grade photo → opens the lightbox (Open in new tab works).
6. Non-quality-edit user: sees the cards read-only (no Add/×); the two endpoints 403.
7. Cross-impact: scoring tiles, check cards ("X/10"), Add Check, Failure Points panel, and Other Notes all still work as before.

---

**2026-07-21 12:41 — you**

updated the quality photos

---

**2026-07-21 12:41 — you**

Records: docs updated (actionDeleteGradePhoto.md, actionUploadGradePhoto.md, stock-quality-grade-photos.md); tech-session none-needed; status-note none-needed.
