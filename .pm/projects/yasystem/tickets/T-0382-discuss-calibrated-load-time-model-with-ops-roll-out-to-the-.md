---
id: T-0382
title: Discuss calibrated load-time model with ops + roll out to the turnaround eyes
type: chore
state: triaged
created: 2026-06-15T17:40:14Z
updated: 2026-06-15T17:40:14Z
project: yasystem
section: null
parent: T-0374
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Calibrated load-time model reviewed and signed off with ops (constants/cap, weight term?)
  - Unload model decided (confirm off-load-curve, or find real unload signal)
  - If agreed, getLoadUnLoadMinutes updated so the turnaround eyes match the visualiser
  - "Cross-impact screens re-checked: run planner, warehouse handover/view-handover, runs-overview(+mobile), RunsOverview"
out_of_scope: []
code_anchors:
  - path: common/models/YaRuns.php
    symbol: estLoadUnloadMinutes
  - path: common/models/YaRuns.php
    symbol: getLoadUnLoadMinutes
  - path: sql/turnaround-load-time-analysis.sql
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - turnaround-visualiser
attention: null
version: 1
---

## Problem

Analysis of real loading dwell-time (status_change_log status 10, 1,104 runs over 6 months — see sql/turnaround-load-time-analysis.sql) shows the legacy `getLoadUnLoadMinutes` formula (5 + 1×vol% + weight/100) is wrong at both ends: it **under-estimates small loads and over-estimates large ones**.

| Volume | runs | actual | legacy formula |
|---|---|---|---|
| ~24% | 363 | 71m | 32m (under ~2×) |
| ~75% | 254 | 111m | 90m |
| ~121% | 156 | 146m | 140m (≈) |
| ~177% | 122 | 144m | 202m (over) |
| ~231% | 125 | 169m | 259m (over) |
| ~377% | 17 | 174m | 413m (over ~2.4×) |

Loading has a large fixed base (~60 min) and scales sub-linearly, plateauing ~3h. A calibrated model `min(180, 60 + 0.45 × volume%)` fits well.

## Status

The turn-around visualiser already uses the calibrated model (YaRuns::estLoadUnloadMinutes; visualiser endpoint passes model='v2'). The finding is shown in an in-page popup. The legacy formula is UNCHANGED everywhere else (the turnaround "eyes" on run planner + warehouse screens) pending this discussion.

## To do

- Review the calibrated model with ops; agree constants/cap (and whether to fold in a weight term).
- Decide on the unload model — there's NO unload dwell data (runs don't pass through status 60), so unload is currently modelled off the load curve on end vol; confirm or find the real unload signal.
- If agreed, roll the model into getLoadUnLoadMinutes so the eyes everywhere match the visualiser (mind cross-impact: run planner, warehouse handover/overview, RunsOverview).