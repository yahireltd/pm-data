---
id: T-0602
title: "Split jobs: assign item lists per piece at sketch time, not only at finalise"
type: feature
state: triaged
priority: p1
created: 2026-07-16T17:02:00Z
updated: 2026-07-16T17:29:12Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 9216
reporter: null
assignee: null
acceptance_criteria:
  - Expanding a split piece on the sketch shows its item list (name + qty)
  - For a plan loaded from a logistics pre-split (preserved split), the item lists match what logistics actually assigned per piece
  - For solver-made splits the provisional distribution shown at sketch time matches what finalise then writes
  - Finalise output unchanged (byte-identical run contracts for the same plan)
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: sketch route/stop build (stops around line 237) + finalise item distribution
  - path: common/services/CopyPlanToRunService.php
    note: pickDeliberateItemDistribution (T-0505) — the logic to reuse at sketch time
  - path: backend/views/route-planner/sketch-planner.php
    note: stop expand panel — render item list
relates:
  - meeting:M-001
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - route-planner
  - release-blocker
attention: null
version: 5
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
Split pieces on the sketch board show weights/volumes but no item lists — items are only distributed to pieces at finalise. Planners can't see what's actually ON piece 1 vs piece 2 while planning, which is confusing — especially when loading a live plan that logistics pre-split (their deliberate item choices exist but the sketch doesn't show them).

## Fix shape
Run the same item-distribution logic used at finalise (pickDeliberateItemDistribution / the T-0505 preserved-split machinery) at sketch-build time so each piece carries its item list for DISPLAY. Preserved/logistics splits show their actual recorded items; solver-made splits show the provisional distribution the finalise would make (label it provisional). Finalise stays the source of truth — no behaviour change to what gets written, this is projection/display.