---
id: T-0579
title: Sprint header "done / committed" is misleading — shows capacity as committed, hides the real ticket count
type: bug
state: triaged
created: 2026-07-15T11:03:02Z
updated: 2026-07-15T11:03:02Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: dogfood
assignee: null
acceptance_criteria:
  - The sprint header shows the actual number of tickets committed to the sprint (committed_items count), not the capacity mislabelled as 'committed'.
  - Capacity / velocity target is shown separately and labelled clearly (e.g. 'target' or 'capacity'), not as 'committed'.
  - The done count reads sensibly against the committed count (e.g. '8 done / 11 committed') and never produces a done-greater-than-committed nonsense like '8 / 5'.
  - The header makes it obvious how many tickets are in the sprint.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sprints
  - ui
  - dogfood
attention: null
version: 1
---

## Problem
The sprint header shows e.g. **"8 / 5"** labelled **"done / committed"**, but the **5 is the sprint's committed *capacity*** (the velocity target set at planning), **not** the number of tickets committed to the sprint. So with 8 done against a target of 5 it reads "8 / 5" — done exceeding "committed", which looks broken. Worse, the **actual committed-ticket count (11) is shown nowhere**, so you can't tell how many tickets are in the sprint.

## Repro
**SPR-032 "Zsolt Features":** 11 tickets committed, capacity 5, 8 done → header renders **"8 / 5 · done / committed"**.

## Expected
- Label the real **committed-ticket count** (11) as "committed".
- Show **capacity / target** (5) separately and clearly labelled as capacity/target.
- e.g. **"8 done · 11 committed · target 5"** — so the total in the sprint is visible and no nonsensical ratios appear.

## Root cause / anchors
The sprint header/card conflates **`velocity_committed` / capacity** with **`committed_items.length`** and labels the capacity as "committed".
- `web/app/_components/SprintsList.tsx` (and/or the sprint header/card that renders the "X / Y done / committed" counts) — confirm exact component on pickup.
- Branch note: sprint features live on **`master`**.