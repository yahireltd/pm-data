---
id: T-0647
title: "Async solve dispatch: queue the job, poll for status, no long-held HTTP request"
type: feature
state: triaged
created: 2026-07-22T16:21:20Z
updated: 2026-07-22T16:21:20Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - Solve click returns in under a second; a running card shows who/when/expected duration
  - Closing the tab does not orphan the result — reopening the date shows the running card, then the result
  - Duplicate solves for a date are refused off queue state; the 4-slot cap enforced the same way
  - Apache/Guzzle/FPM timeout settings no longer affect solving
  - History page shows queued/running/completed states live
out_of_scope: []
code_anchors:
  - path: common/components/RoutePlannerPlanService.php
    note: run() lock wrapper migrates to queue-state checks; the synchronous Guzzle call moves to the worker
  - path: backend/controllers/RoutePlannerController.php
    note: actionIndex solve branch becomes enqueue + redirect; new status-poll endpoint
  - path: backend/web/js/SketchPlanner.js
    note: reSolve() fetch replaced by enqueue + poll; Pong modal becomes a status card
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - solver
  - architecture
  - post-release
attention: null
version: 1
---

## Problem

Solving is still a synchronous ~3.5-minute HTTP request: the browser holds a connection, Apache must be configured to wait (ProxyTimeout 600), an FPM worker is pinned for the duration, and a closed tab orphans the result (it lands in history but the user never sees it). The 22 Jul incident (T-0644) was mitigated with locks/caps/timeouts, but the architecture remains fragile — every timeout knob has to stay in agreement.

## Fix shape

Clicking solve creates the solver_runs row immediately (status 'queued'), dispatches the solver call from a background worker (or the solver box pulls from a queue), and returns instantly. The sketch/history page polls: a "solving… started 12:03 by Daniel, ~4 min" card replaces the Pong-while-you-wait modal, flips to the result when done, and survives tab closes/reloads. The per-date lock and 4-slot cap (T-0644) migrate naturally onto queue state (a queued/running row IS the lock). Removes the ProxyTimeout/Guzzle/FPM timeout coupling entirely.