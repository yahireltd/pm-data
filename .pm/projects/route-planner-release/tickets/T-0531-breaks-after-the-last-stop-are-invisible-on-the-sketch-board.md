---
id: T-0531
title: Breaks after the last stop are invisible on the sketch board; prefer mid-route break placement
type: bug
state: in_progress
created: 2026-07-08T18:13:37Z
updated: 2026-07-08T18:13:53Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "Re-solve 13 Jul: DVL/DDU-style routes show a visible break banner after their last stop matching the header's break total"
  - END bar shows the return drive time and depot arrival so route totals add up by eye
  - Breaks on normal multi-stop routes place mid-route (between stops) whenever such a slot is legal
  - "Replay of run #2495 (10 Jul) still 0 drops"
out_of_scope: []
code_anchors:
  - path: backend/assets/RoutePlannerPython/solver_td.py
    note: _schedule_break — tier the candidate loop
  - path: backend/web/js/SketchPlanner.js:2010-2045
    note: hasBreakBefore/getBreakBefore — add trailing-break + return-leg helpers
  - path: backend/views/route-planner/sketch-planner.php:619-630,802
    note: stops loop banner + END bar
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260708-1813
    model: claude-fable-5
    started: 2026-07-08T18:13:53Z
    status: in_progress
labels:
  - route-planner
  - solver
  - ui
attention: null
version: 3
---

## Problem
On run #2503 (13 Jul) two van routes (FX19 DVL, FX69 DDU) show "30m break" in the run header but NO break row on the board, and their 9h+ totals don't seem to add up from the visible stops. Austin read this as a regression from the T-0527/T-0530 break work and asked to undo it.

## Diagnosis (from the saved run data)
The breaks are real at-stop breaks placed after the LAST customer stop (12:53 at the New Forest customer, 13:30 at Salisbury) — the placement newly enabled by T-0527's "drive home counts as work after the break" fix, and the ONLY legal slot on those routes (all mid-route slots fail the 10:00 floor / 3h-active gates, and delaying the dispatch breaks the tight morning windows). Only one break on the whole run is mid-drive (the 18T, 14:42, with its memo showing correctly). Two genuine defects:
1. The board renders break banners only BETWEEN stops (hasBreakBefore keys on after_stop_idx+1 === sIdx), so an after-last-stop break renders nowhere → header/board mismatch.
2. The long return leg (2h30+ from the New Forest) is also invisible — no line between the last stop and END — so route totals look wrong when summed by eye.
3. Break placement candidates all compete on cost in one pool, so an end-of-route break could in principle beat a legal mid-route slot on pennies.

## Fix
- Solver: tier the placement — mid-route undelayed first (pre-change behaviour), then mid-route with delayed dispatch, then after-last-stop, then mid-drive. Lower tiers only when a higher tier has no legal slot.
- UI: render a trailing break banner after the last stop ("Xm break HH:MM-HH:MM — then drive home"), and show the return-leg drive time + depot arrival on the END bar.
No undo: reverting would re-drop the far jobs (the break-at-far-customer placement is what makes them plannable).