---
id: T-0640
title: "Quality checks: quantity-graded check (qty per grade, weighted average, unchecked, detailed view)"
type: feature
state: review
created: 2026-07-22T11:02:37Z
updated: 2026-07-23T08:50:37Z
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
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-23T05:36:35Z
    ended: 2026-07-23T08:35:11Z
    summary: "Reworked the product quality check so it's now a full snapshot of stock condition instead of a single 1–10 score. When someone runs a check, the system records how many units are at each grade (Good as new / Good / OK / Needs replacing) with up to three optional photos per grade and one note, and it captures the total stock count at that moment. The overall score is a quantity-weighted average of the graded units, and anything not looked at shows as \"Unchecked\" — so a check can be done partially. The Quality tab's summary tiles now reflect the most recent check (average score, how much was checked vs unchecked, current stock with a flag if it has changed since the check, and who checked it, plus a running total of all checks). Clicking a check opens a clear visual breakdown with a grade-distribution bar and the photos. Checks can be edited, deactivated (kept as history) and deleted. Saving a new check automatically retires the previous one so there's one current check at a time. Why: Ben asked to grade stock by quantity at each condition level rather than one blanket score, giving a far more accurate and actionable picture of a product's real condition. Without it, a single number hid how much stock was actually good vs needing replacement. Existing older single-score checks are preserved as history."
    test_plan: |-
      Prereq: the three tables must exist (DDL from Phase 1 — Zsolt already ran it on the dev DB). Test on a live-stock product (e.g. stockID 116) → Quality tab.

      Happy path — add:
      1. Click "Add Check". Confirm the modal is wide with all 4 grades in one row, and "Total stock (today)" is pre-filled from live stock.
      2. Enter qtys across grades (e.g. 12/8/10/3). Watch "Graded" and "Unchecked" update live as you type.
      3. Add a note, Save. Confirm the section re-renders and a new card appears with the weighted score, colour chips per grade, a photo-count badge, and the graded/unchecked line.

      Score check: with 12/8/10/3 the Average Score tile should read ~7.1 (= (10×12+7×8+5×10+3×3)/33).

      Over-limit guard:
      4. In Add Check, enter grade qtys that total MORE than total stock. Confirm a red message appears, Unchecked shows 0, and Save is greyed/blocked. Drop the numbers back below stock → message clears, Save re-enables.

      Tiles:
      5. Confirm 4 wide tiles + 1 small Total Checks. Change the product's live stock, reload → "Stock Now" should flag "was X at last check" in red.

      Detail view:
      6. Click a check card (whole card is clickable, green hover). Confirm the detail modal: score band badge, distribution bar + legend, one row per grade (qty + photos), Unchecked row, note. Click a photo → lightbox. Confirm the modal scrolls internally and doesn't run off-screen.

      Edit:
      7. On a card → Edit. Confirm qtys + note are pre-filled and existing photos show with a red × (click toggles remove/undo). Change a qty, Save → card updates, score recalculates, original stock snapshot is unchanged.

      Auto-deactivate + Total Checks:
      8. Add a second check. Confirm the previous one flips to Inactive (greyed) and the new one is the only Active; Total Checks counts BOTH.
      9. Reactivate an older check via its Deactivate/Activate button → confirm tiles switch to reflect whichever is the latest active.

      Delete:
      10. On a card → Delete → confirm the SweetAlert, then Delete. Card disappears; verify DB rows for that check are gone across the three tables. (Note: on local, S3 photo uploads fail by design — test photo uploads on a deployed env; test photoless flows locally.)

      Permissions: with a user lacking quality-edit rights, confirm none of the Add/Edit/Deactivate/Delete buttons render and the endpoints reject.

      Cross-impact — legacy checks: confirm any pre-existing single-score checks still render in their original card layout (score tile + notes + photos) and still count in Total Checks. Cross-impact — the Quality section is re-rendered by several actions (save, edit, delete, toggle, add/save note, and all failure-point actions); click through Failure Points and Overall Quality Notes to confirm they still render correctly after this rework.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: New docs/features/stock-quality-quantity-graded-check.md; superseded pointers added to stock-quality-check-photos.md (T-0637) and stock-quality-check-active-and-grade-fill.md (T-0639).
labels:
  - quality-management
  - stock
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-23T08:35:11Z
version: 14
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

**2026-07-23 08:34 claude-code:** **2026-07-23 — Build complete, all 6 phases (working tree, lint clean).**

**Phases delivered:** (1) data + models, (2) qty-graded entry modal + save, (3) latest-check summary tiles, (4) qty-graded card + click-to-open detailed view, (5) edit / deactivate / delete, (6) RBAC + docs.

**Additions / decisions made during build (beyond the original AC):**
- **Grade label reworded** "Needs replaced" → **"Needs replacing"** everywhere (single source in `grades()`; DB key `needs_replaced` unchanged).
- **One check current at a time:** saving a new check **auto-deactivates the previously-active one** (older stay as reactivatable history). The **Total Checks** tile counts the **whole history** (active + inactive), not just active.
- **Atomic, all-or-nothing save/edit:** photos upload **first**; only if all succeed does the DB write happen in a single transaction (header + grade rows + photo rows). Any failure rolls back and cleans up uploaded files. **Photos are optional** — a check with none still saves. (Fixes a case where a failed photo upload left a partial check.)
- **Over-limit guard:** if graded qty exceeds total stock, saving is blocked — live red message + disabled Save on the client, and a server-side 400. Unchecked floors at 0.
- **Edit keeps the original stock snapshot** (doesn't re-snapshot on edit); supports removing existing photos and adding new ones (max 3 kept+new per grade).
- **Delete is a hard delete** (header + grade rows + photos) + S3 file cleanup; the S3 cleanup is **prefix-guarded** to the `stock-quality-checks/` folder so reused images from other folders can't be deleted.
- **RBAC:** all writes (add/edit/deactivate/delete) gate on the single `edit['quality']` permission — **delete = edit** (no separate delete permission, per Zsolt); read-only detail view is ungated.
- **UI polish** (Zsolt-directed): wider `lg` entry modal (4 grades in one row), detail-view redesign (score band badge + 0–10 bar, stacked distribution bar with legend, per-grade rows with coloured left edge + units badges + photo lightbox), photo-count badge on the card, whole-card clickable with green hover tint, subtle colour-matched borders on all pills/badges, viewport-capped scrolling modal.

**Docs:** new `docs/features/stock-quality-quantity-graded-check.md`; "superseded" pointers added to the T-0637 and T-0639 docs.

**Delivery:** working-tree only (no commit/push, per project policy) — Zsolt runs the DDL and deploys.

**2026-07-23 08:35 claude-code:** Run run-20260723-0536 completed — Reworked the product quality check so it's now a full snapshot of stock condition instead of a single 1–10 score. When someone runs a check, the system records how many units are at each grade (Good as new / Good / OK / Needs replacing) with up to three optional photos per grade and one note, and it captures the total stock count at that moment. The overall score is a quantity-weighted average of the graded units, and anything not looked at shows as "Unchecked" — so a check can be done partially. The Quality tab's summary tiles now reflect the most recent check (average score, how much was checked vs unchecked, current stock with a flag if it has changed since the check, and who checked it, plus a running total of all checks). Clicking a check opens a clear visual breakdown with a grade-distribution bar and the photos. Checks can be edited, deactivated (kept as history) and deleted. Saving a new check automatically retires the previous one so there's one current check at a time. Why: Ben asked to grade stock by quantity at each condition level rather than one blanket score, giving a far more accurate and actionable picture of a product's real condition. Without it, a single number hid how much stock was actually good vs needing replacement. Existing older single-score checks are preserved as history.

**2026-07-23 08:50 claude-code:** **2026-07-23 — Post-review follow-ups (working tree, lint clean):**

- **Photo-count indicator:** each grade's photo picker now shows "N files selected" as you choose (Add + Edit modals), turning red with "— max 3" if you overshoot.
- **Photo limit hardened to 3 per grade at every layer** (was: client error on save + server *silently* capping to 3):
  - **Live:** the Save button now disables (with a red message) the moment any grade would exceed 3 photos — counting kept existing + newly selected in the Edit modal — alongside the existing graded-over-stock guard.
  - **Click-time:** the SweetAlert block on save remains.
  - **Server:** both save and edit now **hard-reject** with a 400 if a grade exceeds 3, instead of silently dropping the extras.
- **Modal header fix:** opening "Add Check" now reads **"Add Quality Check"** (previously mislabelled "Edit Quality Check"); the Edit modal still reads "Edit Quality Check".

Note for review: these touch the same two files already in scope (StockController.php, view-product-info.php) — no new files.
