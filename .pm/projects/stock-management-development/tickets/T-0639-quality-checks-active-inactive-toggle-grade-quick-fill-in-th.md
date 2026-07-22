---
id: T-0639
title: "Quality checks: active/inactive toggle + grade quick-fill in the Add Check modal"
type: feature
state: review
created: 2026-07-22T08:32:41Z
updated: 2026-07-22T11:51:50Z
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
  channel: Ben feedback on the quality changes
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Each quality check has an active/inactive toggle (default active). Existing checks stay active."
  - "[x] Inactive checks are excluded from all quality summary stats — Average Score, Last 3 Months, Last Check, and Total Checks all count active only."
  - "[x] Inactive check cards remain in the list, greyed with an 'Inactive' badge and a control to reactivate."
  - "[x] The Add Check modal shows 4 grade quick-select buttons; each shows the grade label + score range + the number it fills, and clicking it auto-fills the score (Good as new 10 / Good 7 / OK 5 / Needs replaced 3). The number field stays manually editable."
  - "[x] No in-place edit of a check's score/notes — corrections are done by deactivating and adding a new check."
  - "[x] Everything gated by the same quality-edit permission as today."
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: buildQualitySectionContent (summary tile calcs — filter to active) + renderQualityCheckCard (grey/badge + toggle) + the Add Check modal build (~L18824) + a new actionToggleQualityCheckActive
  - path: ya-hire/common/models/StockQualityCheck.php
    note: new `active` field (default 1)
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: Add Check modal grade buttons + auto-fill JS; check active toggle JS
relates:
  - T-0553
  - T-0637
  - T-0640
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260722-1011
    started: 2026-07-22T10:11:35Z
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-22T10:11:35Z
    ended: 2026-07-22T10:11:50Z
    summary: Two improvements to quality checks, from Ben's feedback. First, a check can now be switched off ("inactive") instead of deleted — it stays on the record as a log but stops counting toward the product's average score; a confirmation spells out the effect, and active/inactive is clear at a glance (green/red pill, greyed score). Second, when logging a check you can now click a grade (Good as new / Good / OK / Needs replaced) to auto-fill the score, so scoring lines up with the grades while still allowing a manual number. Without this, checks were add-only with a bare 1–10 number, and old/irrelevant checks would drag the average with no way to set them aside.
    test_plan: |-
      1. Run the migration (sql/stock_quality_checks_active.sql) — existing checks stay active; tiles unchanged.
      2. Add Check → click a grade button (e.g. Good → fills 7); the picked button highlights blue; you can still type a different number; save works.
      3. On a check → Deactivate → confirm dialog appears → card greys the score tile + shows a red "Inactive" pill; Average / Last 3 Months / Last Check / Total Checks all recalc excluding it; the card stays in the list.
      4. Activate it again (confirm) → tiles revert; green "Active" pill.
      5. A product whose only checks are inactive → tiles show "—"/0 but the checks still list (greyed).
      6. Non-quality-edit user: no Activate/Deactivate button; the toggle endpoint 403s.
      7. Cross-impact (shared Quality section): check photos, grade cards, failure points, and overall notes all still work. Note: the failure-points card also got cosmetic polish this batch (see T-0554) — re-check it renders/scrolls fine.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: docs/features/stock-quality-check-active-and-grade-fill.md
labels:
  - quality-management
  - stock
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-22T10:11:50Z
version: 14
---

## Source
Ben's feedback on the shipped quality changes (email, 22 Jul). Two of his asks, agreed with Zsolt in the reply:

## What
1. **Active / inactive on quality checks** — instead of deleting a check, mark it **inactive**: it drops out of the grade/average but stays on the record as a log. (No hard delete, no in-place edit — a correction = deactivate + add a new check.)
2. **Grades in the Add Check modal** — grade quick-select buttons (Good as new / Good / OK / Needs replaced). Each button **shows the grade label + its score range + the number it fills**, and clicking it **auto-fills the score**; the number field stays manually editable. Points (Ben's, within our existing ranges): **Good as new 10, Good 7, OK 5, Needs replaced 3**.

## Decisions (with Zsolt)
- Inactive checks are **excluded from every summary tile** — Average Score, Last 3 Months, Last Check, Total Checks all count **active only**.
- Inactive check cards **stay in the list**, greyed with an **"Inactive" badge** + a control to reactivate.
- **No edit** of a check's score/notes.

## Out of scope (separate / needs scoping)
- **Quantity-weighted grades** (Ben's bigger ask — record qty at each grade, weight the average). New concept, not in the notes/tickets — to be scoped separately, not bolted on here.
- **Actions → decision board** (future; ties to the Orders/decision-board spike).

## Conversation

**2026-07-22 08:43 claude-code:** **Built (working tree, not committed).**

**Active / inactive checks**
- `active` column on `stock_quality_checks` (default 1) — `sql/stock_quality_checks_active.sql` (run once; existing checks stay active). Added to `StockQualityCheck` rules.
- Summary tiles (Average, Last 3 Months, Last Check, Total Checks) now compute from **active checks only**; the list still shows **all** checks.
- Inactive cards greyed + **"Inactive" badge**; **Activate/Deactivate** button (when quality-editable). New `actionToggleQualityCheckActive` (`stock/toggle-quality-check-active`), re-renders the section. No in-place edit.

**Grade quick-fill in Add Check modal**
- 4 grade buttons above the score — each shows **label + range + the number it fills**; click auto-fills the Score (still editable). Points on `StockQualityGradePhoto::grades()`: Good as new 10 / Good 7 / OK 5 / Needs replaced 3.

**Out of scope (parked):** quantity-weighted grades; decision-board actions.

**RBAC:** `stock/toggle-quality-check-active` — same `quality` edit gate + `stock/*`.

**Test plan (human review):**
1. Run `sql/stock_quality_checks_active.sql`. Existing checks unaffected (active); tiles unchanged.
2. Add Check → click a **grade** button (e.g. Good = 6–7 → 7) → Score fills 7; can still type a different number; save works.
3. On a check → **Deactivate** → card greys + "Inactive" badge; **Average Score / Last 3 Months / Last Check / Total Checks all recalc excluding it**; the card still shows in the list.
4. **Activate** it again → tiles revert.
5. A product with only inactive checks → tiles show "—"/0 but the list still shows the (greyed) checks.
6. Non-quality-edit user: no Activate/Deactivate button; endpoint 403s.
7. Cross-impact: check photos (T-0637), grade cards, failure points, notes all still work.

**2026-07-22 10:11 claude-code:** **UI refinements after the build (all lint clean, working tree):**
- **Activate/Deactivate** now shows a **SweetAlert confirm** spelling out the effect — deactivate: "kept as a record but will NOT count towards the average score" (red); activate: "will count towards the average score again" (green).
- Every check shows a **status pill** — green **Active** / red **Inactive**. Inactive no longer dims the whole card; instead the **score tile greys** (blue `#f0f6ff/#0a5eb8` → grey `#f0f0f0/#999`).
- **Grade quick-fill buttons** redesigned: **label** + **(range)** under it + the **score it fills in the top-right corner** (blue, slightly larger). Clicking **highlights the button blue** and fills the score; ranges are no longer crammed onto the button.

**2026-07-22 10:11 claude-code:** Run run-20260722-1011 completed — Two improvements to quality checks, from Ben's feedback. First, a check can now be switched off ("inactive") instead of deleted — it stays on the record as a log but stops counting toward the product's average score; a confirmation spells out the effect, and active/inactive is clear at a glance (green/red pill, greyed score). Second, when logging a check you can now click a grade (Good as new / Good / OK / Needs replaced) to auto-fill the score, so scoring lines up with the grades while still allowing a manual number. Without this, checks were add-only with a bare 1–10 number, and old/irrelevant checks would drag the average with no way to set them aside.
