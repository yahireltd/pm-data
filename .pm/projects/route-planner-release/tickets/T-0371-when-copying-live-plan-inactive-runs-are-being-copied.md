---
id: T-0371
title: When copying live plan inactive runs are being copied
type: feature
state: done
priority: p2
created: 2026-06-15T13:40:00Z
updated: 2026-07-16T18:37:05Z
project: route-planner-release
section: null
parent: null
children: []
order: 2048
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Do not copy inactive runs to the sketch planner (these are essentially deleted runs)"
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php:2105-2108
    note: syncPlanFromRuns — loads active=1 runs only, wholesale-replaces plan routes
  - path: common/services/SketchPlanService.php:785-788
    note: hydrate-from-runs count — active=1
  - path: common/components/RoutePlannerNew.php:128-129
    note: solver run query — active=1
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260716-1835
    model: claude-fable-5
    started: 2026-07-16T18:35:55Z
    status: completed
    ended: 2026-07-16T18:36:13Z
    summary: "Verified this is already fixed — no code change needed. Austin wasn't sure whether inactive (deleted) runs still got copied from the run planner into the sketch. Checked every path that loads live runs into a sketch: all of them select active runs only, and the sync replaces the sketch's routes wholesale from that active-only set, which also purges any route whose run was deactivated since the last load. Proved it empirically on the sandbox: took a real date carrying 10 active and 2 inactive runs, ran the live-plan sync against a sketch inside a rolled-back transaction — the resulting plan contained exactly the 10 active runs; both inactive run ids were excluded. The fix most likely landed with the live-mirror/sync rework after this ticket was filed."
    test_plan: |-
      1. Sandbox spot-check in the UI: pick 27 Jul 2026 (has 2 inactive runs: ids 24934, 24936 among 10 active) — open the sketch for that date: 10 routes, none matching the deleted runs.
      2. Live workflow check: delete (deactivate) a run on the run planner, reopen the sketch for that date — the deleted run's route disappears from the board on the next load/sync.
      3. Regression guard: the empirical test script is preserved at /tmp/t0371_test.php on the sandbox box (runs inside a rolled-back transaction, changes nothing) — rerun any time with `php /tmp/t0371_test.php`.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 12
branch: PickingSketchSalesDashFriday
---

## Problem

_When copying the live plan from run planner to sketch planner the runs where active = 0 get copied_

##

## Conversation

**2026-07-16 18:36 claude-code:** Run run-20260716-1835 completed — Verified this is already fixed — no code change needed. Austin wasn't sure whether inactive (deleted) runs still got copied from the run planner into the sketch. Checked every path that loads live runs into a sketch: all of them select active runs only, and the sync replaces the sketch's routes wholesale from that active-only set, which also purges any route whose run was deactivated since the last load. Proved it empirically on the sandbox: took a real date carrying 10 active and 2 inactive runs, ran the live-plan sync against a sketch inside a rolled-back transaction — the resulting plan contained exactly the 10 active runs; both inactive run ids were excluded. The fix most likely landed with the live-mirror/sync rework after this ticket was filed.

---

**2026-07-16 18:37 — you**

done
