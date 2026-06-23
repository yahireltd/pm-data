---
id: T-0374
title: Vehicle turn-around visualiser (3-day fixed-position view)
type: feature
state: done
created: 2026-06-15T15:21:22Z
updated: 2026-06-23T12:04:27Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Perspective date selector drives a 3-column Day before / Day / Day after view
  - All fleet + hire-pool vehicles listed in fixed position across columns, grouped by type, empty rows shown, ignore-flagged vehicles excluded
  - Each run rendered with the reused run-planner card (kg, vol, start/end weight, times, status, eyes)
  - Linking element between consecutive runs shows warehouse-open turnaround duration + weight/vol to turn around, reusing the existing turnaround thresholds (orange 3-4h, red <=3h)
  - Warehouse open/close hours configurable (default 06:00-22:00) instead of hardcoded
  - v1 is read-only (no reassignment)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - concept
attention: null
version: 3
---

## Problem

Ops wants a way to see, at a glance, whether each vehicle can realistically turn around between runs across consecutive days — i.e. is there enough warehouse-open time (after unloading the returning run and loading the next) before the next dispatch. Today the only signal is the small "turnaround eyes" icon on the run planner; there's no whole-fleet, multi-day overview.

(Concept reference held by Austin: desktop/2025/ops vehicle turn around visualiser xls.)

## Concept (from ops)

1. User sets a perspective date — shown as the middle of three columns: Day before / Day / Day after.
2. Every vehicle is listed in a fixed position across all three columns (so you scan a row left-to-right), grouped by vehicle type. Multiple runs per vehicle per day are shown.
3. Each run reuses the run-planner run card (kg, vol, start/end weight, dispatch–return times, status, turnaround eyes). Tight turnarounds are highlighted (red background on the card/link when tight).
4. A linking element between consecutive runs shows the warehouse-open duration available to turn around, plus the weight/vol that has to be unloaded then reloaded.
5. (Later) Reassign a run to a more suitable vehicle directly from this view — except runs already loaded / on the road.

## Key technical finding — the turnaround engine already exists

`YaRuns::checkTurnaroundNext()` / `checkTurnaroundPrev()` (common/models/YaRuns.php:2115–2188) already compute the whole thing and run in production behind the existing eyes icon:

- usable turnaround = `calculateWorkingMinutes(prevReturn → nextDispatch)` − next-run load minutes − prev-run unload minutes
- `calculateWorkingMinutes()` already counts only warehouse-open time — currently **hardcoded 06:00–22:00**
- load/unload time-from-weight comes from `getLoadUnloadMinutes('Load'|'Unload')`
- return time = `backAtBaseTime ?? endDateTime`; next dispatch = `startDateTime`
- `getNextRun()` / `getPreviousRun()` give same-vehicle chronological runs across day boundaries

Existing thresholds (these are the "tight" rule to reuse, not reinvent):
- > 240 min (>4h): normal (blue eye)
- 181–240 min (3–4h): tight (orange eye)
- ≤ 180 min (≤3h): very tight (red, flashing eye)

Today these methods return only an icon+tooltip. Plan: refactor them to also return the raw figures (usable mins, load/unload mins, weight/vol, colour band) so the existing eyes AND the new visualiser link element share one source of truth.

## Decisions captured (Austin)

- Warehouse hours: fixed but configurable (lift the hardcoded 06:00–22:00 into a setting).
- Tight rule: reuse the existing turnaround-eyes timings (above).
- v1 scope: read-only first; reassignment is a fast-follow (v2).
- Vehicle list: active fleet + hire pool, grouped by type, honouring the ignore-vehicle flag, incl. empty "NO RUN SET" rows.

## Reuse map

- Run card: `runPlannerRun` element, YaRuns.php:545
- Turnaround maths: YaRuns checkTurnaroundNext/Prev + calculateWorkingMinutes + getLoadUnloadMinutes
- Lock/"can't reassign loaded/on-road" rule (for v2): `YaRuns::operationallyLockedCondition()` + Itemcountlog type-2 loaded check
- Layout app: model on the Vue 3 sketch planner

## Open question

How much is `getLoadUnloadMinutes` trusted operationally? It decides whether the link element leads with time-only or time+load.

## Out of scope (v1)

Reassignment / drag between vehicles (v2).

## Conversation

**2026-06-15 15:32 claude-code:** **Open question resolved (Austin):** `getLoadUnloadMinutes` has been in use a long time and is "surprisingly reliable".

So the linking element should present the load figure as first-class, not advisory — e.g. "2h to turn around 1.8T / 144%", showing both the usable turnaround time and the weight/vol to handle together. No need to hedge the load number.

With that, the v1 (read-only) spec is fully scoped — no further unknowns. Ready for AP to slot in when prioritised.

---

**2026-06-23 12:04 — you**

done

---

**2026-06-23 12:04 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
