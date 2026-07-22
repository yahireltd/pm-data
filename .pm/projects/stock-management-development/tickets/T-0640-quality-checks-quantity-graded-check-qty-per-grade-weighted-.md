---
id: T-0640
title: "Quality checks: quantity-graded check (qty per grade, weighted average, unchecked, detailed view)"
type: feature
state: triaged
created: 2026-07-22T11:02:37Z
updated: 2026-07-22T11:03:53Z
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
assignee: null
acceptance_criteria:
  - A quality check records the product's total stock (today's count) plus a quantity at each grade (Good as new / Good / OK / Needs replaced), with the remainder shown as 'Unchecked'.
  - Each grade (and Unchecked) supports optional photo(s); one note per check. Entry is a bigger modal and can be submitted partially (some stock left unchecked).
  - The check's score is the quantity-weighted average over graded items only (points 10/7/5/3); the unchecked count is tracked separately and not in the score.
  - The Quality tab shows the weighted average, the total stock, and graded-vs-unchecked counts.
  - "Clicking a check opens a detailed view: qty per grade, photos, and the note."
  - This replaces the single-score check entry (T-0637/T-0639); existing single-score checks are retained as legacy history.
  - The total stock is snapshotted onto each check so historical checks keep their own denominator.
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
agent_runs: []
labels:
  - quality-management
  - stock
attention: null
version: 3
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
