---
id: T-0578
title: "Roadmap: overlapping sprints in the same dev lane hide each other (no vertical stacking)"
type: bug
state: triaged
created: 2026-07-15T10:53:08Z
updated: 2026-07-15T10:53:08Z
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
  - In a dev's lane, sprints that overlap in time are stacked onto separate sub-rows so every bar is fully visible — no bar hidden behind another.
  - The lane's height grows to fit however many sub-rows the overlaps need.
  - Non-overlapping sprints still share a single row (no wasted vertical space when there's no overlap).
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - roadmap
  - ui
  - dogfood
attention: null
version: 1
---

## Problem
On the Roadmap, when a dev has two or more sprints in the **same time frame**, their bars overlap horizontally and hide each other — the later-rendered bar draws on top of the earlier one. (Screenshot: the *Unassigned* lane — "Release sprint 1 — verify fixes…" sits over another bar behind it.) The overlapped sprint is effectively invisible.

## Root cause
`web/app/_components/Roadmap.tsx` renders every sprint in a lane as an `absolute top-0.5 h-6` bar inside a **fixed-height** (`h-7`) `relative` container, positioned only by left/width from its date range. There's no vertical offset for overlapping bars and the row height is fixed, so overlapping bars overdraw.

## Expected
Overlapping bars should **stack vertically** — each overlapping sprint on its own sub-row, under the other — and the lane should grow in height so all sprints in a time window are visible.

## Fix approach (for whoever picks it up)
Greedy interval-partition per lane: sort the lane's sprints by start date; assign each to the **first sub-lane whose previous bar ends before this one starts** (else open a new sub-lane). Then set each bar's vertical offset = `subLaneIndex × rowHeight` and the lane container height = `subLaneCount × rowHeight`.

## Code anchors
- `web/app/_components/Roadmap.tsx` — the lane render block (`laneSprints.map(...)`, the `relative h-7` container, bars `absolute top-0.5 h-6` positioned by `left`/`width`).

## Branch note
The Roadmap feature lives on **`master`** (T-0458), **not** on `facelift/rbac-look` — whoever fixes this should branch off master.