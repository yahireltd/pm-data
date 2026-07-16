---
id: T-0604
title: Finalised runs missing start/end/max weight & volume on the run planner
type: bug
state: triaged
priority: p1
created: 2026-07-16T17:18:29Z
updated: 2026-07-16T17:28:41Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 11264
reporter: null
assignee: null
acceptance_criteria:
  - After finalising a sketch, each created run shows start, end and max weight AND volume on the run planner
  - Figures match the sketch board's START/END bars for the same route (same stop order, same maths)
  - Hand-built (non-sketch) runs are unaffected
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: finalise path writing runs/run contracts
  - path: backend/views/logistics/run-planner.php
    note: run header weight/volume display
  - path: backend/web/js/SketchPlanner.js
    note: routeMetrics computed — the start/end/peak maths to mirror
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
version: 3
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
When a sketch is finalised, the run's weight/volume figures need calculating: contracts show the accrued weight/volume at their deliver/collect point, but the RUN should show start load, end load and peak (max) weight/volume — logistics report this doesn't currently show on runs created from a finalised sketch.

## Notes
The sketch board already computes exactly this per route (routeMetrics: start/end/peak W+V respecting stop order — deliveries reduce load, collections add). The finalise path needs to compute the same numbers and persist/display them on the run planner run header. Check what the run planner shows for hand-built runs vs sketch-finalised runs — the gap may be that finalise never writes whatever field the header reads, or the header derives it from run contracts in an order the finalised data doesn't satisfy.