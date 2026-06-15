---
id: T-0379
title: getLoadUnLoadMinutes uses '=' not '==' — Unload silently returns the Load value
type: bug
state: triaged
created: 2026-06-15T15:52:00Z
updated: 2026-06-15T15:52:00Z
project: yasystem
section: null
parent: T-0374
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - getLoadUnLoadMinutes('Unload') uses end weight/vol; 'Load' uses start weight/vol
  - Comparisons use == ; variables guarded against undefined
  - Turnaround eyes across run planner + warehouse screens reflect corrected (less pessimistic) unload time
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

`YaRuns::getLoadUnLoadMinutes($dir)` (common/models/YaRuns.php:1663) branched on `if ($dir='Load')` / `elseif ($dir='Unload')` — assignment `=`, not comparison `==`. So `$dir` was always reassigned to 'Load', the condition was always truthy, and the Unload branch was dead. Every "Unload" calculation in the system has actually returned the Load figure (start weight/vol instead of end weight/vol).

## Impact

Used by the turnaround "eyes" (checkTurnaroundNext/Prev) shown on the run planner, warehouse handover, runs-overview and runs-overview-mobile. usable_turnaround = working − load(next) − unload(prev); because unload(prev) used the START (loaded) weight instead of the END weight, unload time was over-stated and turnarounds read **tighter/redder than reality** everywhere the eyes appear. Also blocks the new turn-around visualiser from showing a correct unload shoulder.

## Fix

`=` → `==` on both branches; initialise $volumeMinutes/$weightMins to 0 as a guard. After the fix Unload correctly uses end weight/vol, so turnarounds read slightly less tight (more accurate) across all the above screens.

## Cross-impact to re-check

Run planner eyes; warehouse handover / view-handover; runs-overview (+ mobile); RunsOverview model.