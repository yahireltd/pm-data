---
id: T-0639
title: "Quality checks: active/inactive toggle + grade quick-fill in the Add Check modal"
type: feature
state: triaged
created: 2026-07-22T08:32:41Z
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
  channel: Ben feedback on the quality changes
assignee: null
acceptance_criteria:
  - Each quality check has an active/inactive toggle (default active). Existing checks stay active.
  - Inactive checks are excluded from all quality summary stats — Average Score, Last 3 Months, Last Check, and Total Checks all count active only.
  - Inactive check cards remain in the list, greyed with an 'Inactive' badge and a control to reactivate.
  - The Add Check modal shows 4 grade quick-select buttons; each shows the grade label + score range + the number it fills, and clicking it auto-fills the score (Good as new 10 / Good 7 / OK 5 / Needs replaced 3). The number field stays manually editable.
  - No in-place edit of a check's score/notes — corrections are done by deactivating and adding a new check.
  - Everything gated by the same quality-edit permission as today.
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