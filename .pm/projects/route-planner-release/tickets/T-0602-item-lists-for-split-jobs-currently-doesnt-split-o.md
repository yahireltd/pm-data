---
id: T-0602
title: "Split jobs: assign item lists per piece at sketch time, not only at finalise"
type: feature
state: review
priority: p1
created: 2026-07-16T17:02:00Z
updated: 2026-07-16T18:09:25Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 9216
reporter: null
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260716-1807
    model: claude-fable-5
    started: 2026-07-16T18:07:08Z
    status: completed
    ended: 2026-07-16T18:09:25Z
    summary: "Split pieces on the sketch board now show what's actually on each piece. Before, items were only assigned to pieces at finalise, so planners saw two half-stops with weights but no idea which furniture was on which — especially confusing when loading a live plan that logistics had already deliberately split by item. Expanding a split piece now shows that piece's item list with a clear status: green \"as split by logistics\" when the day's runs already carry the real per-piece assignment (their choices, shown verbatim), or amber \"provisional — confirmed at finalise\" for solver-made splits, where the list shown is computed by exactly the same distribution routine finalise will run — so what you see at sketch time is what finalise writes. Non-split stops keep showing the full contract item list. Finalise itself is completely untouched."
    test_plan: |-
      1. Pull branch on sandbox, hard-refresh. Solve a day with Full Luton split, expand both pieces of a split contract (e.g. a (D1)/(D2) pair): each shows a DIFFERENT item list with the amber provisional note, and the two lists together add up to the full contract items.
      2. Finalise that sketch, check the run planner item lists per run-contract row match what the sketch showed (same distribution code — should be identical).
      3. Load a live plan where logistics manually split a contract by items (or make one: split on run planner, assign items, reopen sketch): the pieces show the green 'as split by logistics' note with their actual items.
      4. Non-split stops: expand shows the full contract list exactly as before.
      5. Unassigned-pool split pieces also show their piece list.
      Cross-impact: sketch page load runs the distribution routine per split contract (display only, no writes); finalise output unchanged by construction — verify step 2 to be sure.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - release-blocker
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-16T18:09:25Z
version: 8
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
Split pieces on the sketch board show weights/volumes but no item lists — items are only distributed to pieces at finalise. Planners can't see what's actually ON piece 1 vs piece 2 while planning, which is confusing — especially when loading a live plan that logistics pre-split (their deliberate item choices exist but the sketch doesn't show them).

## Fix shape
Run the same item-distribution logic used at finalise (pickDeliberateItemDistribution / the T-0505 preserved-split machinery) at sketch-build time so each piece carries its item list for DISPLAY. Preserved/logistics splits show their actual recorded items; solver-made splits show the provisional distribution the finalise would make (label it provisional). Finalise stays the source of truth — no behaviour change to what gets written, this is projection/display.

## Conversation

**2026-07-16 18:09 claude-code:** Run run-20260716-1807 completed — Split pieces on the sketch board now show what's actually on each piece. Before, items were only assigned to pieces at finalise, so planners saw two half-stops with weights but no idea which furniture was on which — especially confusing when loading a live plan that logistics had already deliberately split by item. Expanding a split piece now shows that piece's item list with a clear status: green "as split by logistics" when the day's runs already carry the real per-piece assignment (their choices, shown verbatim), or amber "provisional — confirmed at finalise" for solver-made splits, where the list shown is computed by exactly the same distribution routine finalise will run — so what you see at sketch time is what finalise writes. Non-split stops keep showing the full contract item list. Finalise itself is completely untouched.
