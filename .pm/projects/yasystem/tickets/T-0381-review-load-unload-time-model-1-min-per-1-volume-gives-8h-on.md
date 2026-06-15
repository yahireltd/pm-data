---
id: T-0381
title: Review load/unload time model — 1 min per 1% volume gives 8h+ on high-volume runs
type: spike
state: triaged
created: 2026-06-15T17:08:28Z
updated: 2026-06-15T17:08:28Z
project: yasystem
section: null
parent: T-0374
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Decide the correct load/unload time model with ops (volume basis + rates, any cap)
  - Document the agreed model
  - Separate follow-up to implement if the formula changes (mind the cross-impact on the turnaround eyes everywhere)
out_of_scope: []
code_anchors:
  - path: common/models/YaRuns.php
    symbol: getLoadUnLoadMinutes
    lines: 1663-1684
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

`YaRuns::getLoadUnLoadMinutes()` charges buffer(5) + 1 min per 1% volume + 1 min per 100kg. On the turn-around visualiser this surfaced a run (R19) with 462% volume / 4,680kg producing a **8h 34m** load time — dominated by the volume term (462 min from volume alone). Austin flagged this looks too high.

## Questions to resolve

- Is "% volume" here capacity-relative or cumulative across drops? 462% implies it isn't a single-load 0–100% figure, so 1 min/% may be the wrong basis.
- Should load time be based on the initial load only, and/or capped, and/or use a different rate?
- Confirm the intended minutes-per-volume / per-weight rates with ops.

## Notes

This affects the turnaround "eyes" everywhere (run planner, warehouse screens) as well as the visualiser, since they share getLoadUnLoadMinutes. Do NOT change the formula blind — agree the model first. Out of scope of the visualiser build; raised from review of T-0374.