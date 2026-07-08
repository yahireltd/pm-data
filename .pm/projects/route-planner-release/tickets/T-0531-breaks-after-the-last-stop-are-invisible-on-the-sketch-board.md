---
id: T-0531
title: Breaks after the last stop are invisible on the sketch board; prefer mid-route break placement
type: bug
state: done
created: 2026-07-08T18:13:37Z
updated: 2026-07-08T19:01:15Z
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
  - "[x] Re-solve 13 Jul: DVL/DDU-style routes show a visible break banner after their last stop matching the header's break total"
  - "[x] END bar shows the return drive time and depot arrival so route totals add up by eye"
  - "[x] Breaks on normal multi-stop routes place mid-route (between stops) whenever such a slot is legal"
  - "[x] Replay of run #2495 (10 Jul) still 0 drops"
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
    status: completed
    ended: 2026-07-08T18:28:52Z
    summary: "Fixed the \"invisible breaks\" that looked like a regression on run #2503. The two van routes' breaks were real and legally required — placed after their LAST stop (break at the far customer, then the drive home; provably the only legal slot on those routes) — but the sketch board only drew break banners between stops, so the header said \"30m break\" while the board showed nothing. The 2h30+ return drives were equally invisible, which is why the 9-hour totals didn't add up by eye. Two fixes instead of an undo (an undo would re-drop the far jobs): (1) the board now shows a trailing break banner after the last stop, including whether it's at the last stop or mid-drive, plus a return-leg row with the drive time home and depot arrival — totals now add up on screen; (2) solver break placement is now tiered rather than one cost-competitive pool — a mid-route break (the old, expected behaviour) always wins when legal, and delayed-dispatch / end-of-route / mid-drive placements only fire in that order when everything better is impossible. Verified by replay of the 10 July request: still 0 dropped jobs, 16 routes, all 12 breaks legally placed and all now visible."
    test_plan: |-
      1. Pull branch on sandbox, hard-refresh the sketch planner (JS changed), open the saved 13 Jul plan (run #2503): FX19 DVL and FX69 DDU should now show a break banner after their last stop ("30m break 12:53-13:23 — at last stop, then home") AND a return-leg row ("~150m drive back · depot HH:MM"). Header vs board now consistent.
      2. Sum a far route by eye: stops + break + return leg should now reach the header total (~9h17).
      3. Re-solve a normal all-London day: breaks should sit BETWEEN stops as before (tier 1) — no end-of-route breaks unless genuinely forced.
      4. The mid-drive break route (MX67 AZG-style) shows banner + memo + return leg together.
      5. Edge: hand-edit a route (drag a stop) — clearRouteTimings wipes solver breaks; banners and return-leg row should disappear cleanly (returnLegInfo guards on missing timings).
      Open question parked with Austin: the "no breaks before 10:00" floor dates from the original Feb TD-solver build and he doesn't recognise it as policy — drop/keep/soften decision pending; it materially affects how often the lower tiers fire.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - solver
  - ui
attention: null
version: 10
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

## Conversation

**2026-07-08 18:28 claude-code:** Run run-20260708-1813 completed — Fixed the "invisible breaks" that looked like a regression on run #2503. The two van routes' breaks were real and legally required — placed after their LAST stop (break at the far customer, then the drive home; provably the only legal slot on those routes) — but the sketch board only drew break banners between stops, so the header said "30m break" while the board showed nothing. The 2h30+ return drives were equally invisible, which is why the 9-hour totals didn't add up by eye. Two fixes instead of an undo (an undo would re-drop the far jobs): (1) the board now shows a trailing break banner after the last stop, including whether it's at the last stop or mid-drive, plus a return-leg row with the drive time home and depot arrival — totals now add up on screen; (2) solver break placement is now tiered rather than one cost-competitive pool — a mid-route break (the old, expected behaviour) always wins when legal, and delayed-dispatch / end-of-route / mid-drive placements only fire in that order when everything better is impossible. Verified by replay of the 10 July request: still 0 dropped jobs, 16 routes, all 12 breaks legally placed and all now visible.

---

**2026-07-08 19:01 — you**

Done
