---
id: T-0640
title: "Quality checks: quantity-graded check (qty per grade, weighted average, unchecked, detailed view)"
type: feature
state: in_progress
created: 2026-07-22T11:02:37Z
updated: 2026-07-23T06:06:12Z
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
  channel: Ben feedback + notes + mockup (22 Jul)
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - A quality check records the product's total stock (snapshotted at the check's date/time) plus a quantity at each grade (Good as new / Good / OK / Needs replaced); the remainder shows as 'Unchecked' (auto = total − graded, no photo).
  - Each of the 4 grades supports up to 3 optional evidence photos; one note per check. Entry is a bigger modal and can be submitted partially (stock left unchecked is fine).
  - "The check's score is the quantity-weighted average over graded items only (points: Good as new 10, Good 7, OK 5, Needs replaced 3); the unchecked count is tracked separately and not in the score."
  - "The Quality-tab tiles are driven by the LATEST check: four wide tiles — (1) Average Score (weighted), (2) Checked = graded / unchecked against the check's snapshot total, (3) Stock Now = current live stock, flagged if it has drifted from the snapshot ('was X at last check'), (4) Last Check (date + who) — plus one small Total Checks tile. Older checks open as history. (Replaces the old Last 3 Months Score + Total Checks tiles.)"
  - "Clicking a check opens a detailed view: qty per grade, evidence photos, and the note."
  - A check can be edited, deactivated/reactivated (inactive is excluded from the tiles), and deleted.
  - Replaces the single-score check entry (T-0637/T-0639); existing single-score checks are kept as legacy history. The grade-card reference photos (T-0553) are separate guidance and unchanged.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: buildQualitySectionContent (summary tiles + checks list), renderQualityCheckCard, the Add Check modal (~L18872), actionSaveQualityCheck (~L20335), actionToggleQualityCheckActive — all reworked for the qty-graded model
  - path: ya-hire/common/models/StockQualityCheck.php
    note: check header reworked (score derived, totalStock snapshot, active) + NEW models/tables for per-grade qty + per-grade photos
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: bigger Add Check modal + saveQualityCheck JS; detailed-view modal
  - path: ya-hire/common/models/StockQualityGradePhoto.php
    note: grades() point values (10/7/5/3) reused for the weighted average
relates:
  - T-0553
  - T-0637
  - T-0639
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260723-0536
    started: 2026-07-23T05:36:35Z
    status: in_progress
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-23T05:36:35Z
labels:
  - quality-management
  - stock
attention: null
version: 11
---

## Source
Ben's feedback (email 22 Jul) + follow-up notes + a mockup. Evolves the quality check from a single 1–10 score into a **quantity-graded snapshot**. **Supersedes the check-entry parts of T-0637** (photos per check) **and T-0639** (single-score + grade quick-fill + active/inactive).

## Concept
A quality check becomes a **single snapshot** of the product's stock graded by quantity, entered in a **bigger modal**:
- Shows the product's **Total stock (today's count)**, snapshotted onto the check.
- A **qty field per grade** — Nearly new (= Good as new) / Good / OK / Needs replaced — each with **optional photo(s)**.
- An **Unchecked** column = total − sum of graded (the remainder), also with an optional photo.
- **One note** for the whole check.
- Can be **submitted partially** (items left unchecked is fine).

## Mockup (Ben)
Row: `Total stock | Nearly new (qty+cam) | Good (qty+cam) | OK (qty+cam) | Needs replaced (qty+cam) | Unchecked (qty+cam)`.
Example: total **1000** → 56 / 47 / 234 / 345 graded (**682**), **318** unchecked; weighted score = (56×10 + 47×7 + 234×5 + 345×3) ÷ 682 ≈ **4.5 / 10**.

## Scoring & display
- **Score = quantity-weighted average over *graded* items only**: Σ(gradePoint × qty) ÷ Σ(graded qty). Points: **Good as new 10, Good 7, OK 5, Needs replaced 3**.
- **Unchecked count tracked separately** (not in the score) — "how many still to check", usable elsewhere.
- Tiles show: **weighted average**, **total stock**, **graded vs unchecked**.
- **Click a check → detailed view**: exact qty per grade, photos, and the note.

## Open questions (confirm on pickup)
1. **Unchecked qty auto** (= total − graded), with an optional photo?
2. Tiles reflect the **latest check** (others as history), or a blend across checks?
3. Old single-score checks (T-0637/T-0639) kept as **legacy history**; all new checks use this form?
4. **Edit + active/inactive (+ delete)** on a check?
5. **Snapshot the total stock** on each check (its own denominator as stock changes)?
6. **Photos per grade — one, or up to a few** (like the reference cards' up-to-4)?

## Data (sketch)
- Check header: `stockID, totalStock (snapshot), note, checkedBy, checkedOn, active`.
- Per-grade qty rows: `checkID, grade, qty`.
- Per-grade photos: `checkID/gradeRow, photoUrl`. Score + unchecked derived.

## Out of scope (separate)
- **Sales / QB surfacing** (icons/scores) — Ben: "we'll talk about prototype … later".
- **Actions → magic decision board** — future.

## Supersedes / relates
Reworks the check entry from **T-0637** (photos per check → per grade) and **T-0639** (single score → qty per grade); those stay as shipped history, this replaces the entry model going forward.

## Conversation

**2026-07-22 11:03 claude-code:** **Answers (Zsolt):**
1. **Unchecked qty is auto** (= total − graded); **no photo** on Unchecked — photos on the 4 grades only.
3. **New checks use the new quantity-graded form**; old single-score checks kept as legacy history.
4. Support **edit + active/inactive + delete** on a check.
5. **Snapshot the total stock** (the qty at the check's date/time) onto each check.

**Still open:** (2) which check the summary tiles reflect — latest vs blend; (6) photos per grade — one, or up to a few.

**2026-07-22 11:32 claude-code:** **More answers (Zsolt):**
2. **Latest check drives the summary tiles** (headline = the most recent full check's weighted average; total stock + graded/unchecked alongside; older checks openable as history).

**Photo clarification (important):** two separate photo sets —
- **Grade-card reference photos** (T-0553) = *guidance* ("what each grade should look like"), static, unchanged per check.
- **Check per-grade photos** = *evidence* for that specific check (the cam icons in the mockup). New per check, independent of the guidance photos.

**Only #6 left:** how many evidence photos per grade in a check (one, or up to a few).

**2026-07-22 11:33 claude-code:** 6. **Evidence photos per grade in a check: up to 3, optional.** (Unchecked has none.)

All open questions resolved — acceptance criteria finalised above. Ticket is fully specced and build-ready.

**2026-07-22 11:50 claude-code:** **Tiles locked (Option A + small Total Checks):** four wide tiles — **Average Score** (weighted, latest) · **Checked** (graded / unchecked vs snapshot) · **Stock Now** (live, flags drift from snapshot) · **Last Check** (date + who) — plus **one small tile for Total Checks** at the end. Drops the old *Last 3 Months Score*. Layout: 4 × `flex:1` + one narrow fixed-width tile. AC #4 updated.

**2026-07-23 06:06 claude-code:** **Build started (run-20260723-0536). Phase 1/6 — data + models — done (working tree, lint clean).**
- DDL `sql/stock_quality_check_qty_graded.sql`: `totalStock` on `stock_quality_checks` + new `stock_quality_check_grades` + `stock_quality_check_grade_photos`. Legacy checks untouched.
- New models `StockQualityCheckGrade`, `StockQualityCheckGradePhoto`; `StockQualityCheck` gains `totalStock`, `gradeRows`/`gradePhotos` relations, and `gradedQty()`/`uncheckedQty()`/`weightedScore()` helpers.
Next: Phase 2 — bigger entry modal + save.
