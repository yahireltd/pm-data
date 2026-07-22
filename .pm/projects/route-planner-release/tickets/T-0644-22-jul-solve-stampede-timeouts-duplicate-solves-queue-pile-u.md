---
id: T-0644
title: "22 Jul solve stampede: timeouts, duplicate solves, queue pile-up (incident + fix package)"
type: incident
state: done
created: 2026-07-22T16:18:31Z
updated: 2026-07-22T16:22:17Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Duplicate solve for the same date is refused instantly with a named banner (both from the run planner and the sketch, and from a second window of the same browser)"
  - "[x] Two different dates solve in parallel and both complete in-browser in ~3.5 min"
  - "[x] A 5th concurrent solve is refused naming the dates in flight"
  - "[x] No FPM worker held beyond 540s by a solver call"
  - "[x] Post-incident: no recurrence of >2 identical solves for one date in solver_runs"
out_of_scope: []
code_anchors:
  - path: common/components/RoutePlannerPlanService.php
    note: "run() lock wrapper: per-date lock + 4-slot global cap + TTL from tl/time_limit_sec; Guzzle 540s"
  - path: backend/controllers/RoutePlannerController.php
    note: "solve_locked handling: renderSolveBusy, sketch solve_busy redirect, compare-solvers bail"
  - path: backend/web/js/SketchPlanner.js
    note: "reSolve(): _sb cache-buster + no-store fetch"
  - path: backend/views/route-planner/sketch-planner.php
    note: solve_busy banner
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260722-1618
    model: claude-fable-5
    started: 2026-07-22T16:18:43Z
    status: completed
    ended: 2026-07-22T16:19:10Z
    summary: |-
      On the morning after go-live, planners solving two dates at once hit gateway timeouts, retried, and unknowingly stacked 13 duplicate solve jobs that kept the solver busy for over an hour and slowed the whole system — even though every solve actually succeeded invisibly. We reconstructed the entire chain from logs (solver box, database, app log): solves take ~3.5 minutes but the web server gave up waiting at 5 minutes once anything queued, and abandoned requests kept a system worker tied up indefinitely.

      We fixed it at every layer: a second solve for the same date is now refused instantly with a friendly message saying who is already solving it and how long it will take; at most 4 solves can run at once (matching the solver machine's 4 processors, which now genuinely run in parallel instead of one at a time); the web server now waits long enough for a normal solve to return to the browser; abandoned requests free their worker after 9 minutes; and a browser quirk that let the same person accidentally queue a duplicate from a second window was closed. Without these, the first busy morning would have re-triggered the same pile-up, with logistics losing an hour of planning time and the office seeing a slow system.
    test_plan: |-
      On the routing test box (all deployed; live pending its git pull — re-verify items 1-3 on live after pulling):

      1. Solo solve: solve any future date from the sketch → completes in-browser in ~3.5 min, plan renders (no gateway timeout).
      2. Duplicate refusal: while a solve runs, hit Re-solve for the SAME date from a second browser (or second window of the same browser — this specifically tests the coalescing fix) → instant amber banner naming the user, start time and expected duration; NOTHING written (no new solver_runs row).
      3. Parallel dates: solve two different dates simultaneously → both complete in ~3.5 min (check history page shows both, timestamps overlapping).
      4. Capacity refusal: with 4 solves in flight (needs two people or scripted), a 5th gets "solver at full capacity" naming the running dates.
      5. Solver box check: `pgrep -f uvicorn | wc -l` shows parent + 4 workers; journalctl shows overlapping POST /solve-td completions when two dates solve together.
      6. Cross-impact: compare-solvers page still works when no solve is running for the date; console fleet-scenario runners still run (they pass through the lock gracefully).
      7. Live-only after pull: confirm ProxyTimeout 600 present in the vhost (applied 22 Jul by Austin) and a real solo solve returns to the browser.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: written
labels:
  - incident
  - solver
  - performance
attention: null
version: 11
---

## What happened (22 Jul, ~12:00–13:10)

Logistics solved two dates concurrently on live. Every solve takes a fixed ~3m20s (180s solver budget), but Apache's proxy window was 300s and each browser request held a PHP-FPM worker indefinitely (Guzzle timeout=0). One queued solve pushed waits past the window → users saw 504 Gateway Timeout while their solves succeeded invisibly → they retried → 13 solves stacked for 2 dates (8 near-identical for 30 Jul), the single-worker solver box drained the queue serially for ~70 minutes, and pinned FPM workers made the whole app sluggish. All plans completed OK; nothing lost.

Root-cause chain confirmed from three log sources: solver-box journal (completions every ~199s), live solver_runs rows, live app log. Later same-browser testing found a fourth mechanism: Firefox coalesces identical in-flight GETs, so a duplicate solve from another window of the same browser was parked client-side and never reached the per-date lock.

## Fix package (all deployed to routing test box; live pull pending)

1. Per-date in-flight solve lock (cache, TTL scaled to the selected solver budget) — duplicate solve for a date is refused instantly with who/when/expected duration.
2. Global cap of 4 concurrent solves (slot keys matching the solver box's worker count) — 5th solve refused with the list of dates running.
3. Guzzle timeout 0 → 540s (inside FPM's 600s) — workers can't be pinned forever.
4. Apache ProxyTimeout 600 on the live vhost (applied by Austin) — a normal solve now fits the window.
5. Solver box: uvicorn --workers 4 (4 cores, one single-threaded OR-Tools solve each) — two dates genuinely solve in parallel.
6. Cache-buster (_sb) + no-store on the sketch re-solve fetch — same-browser duplicates reach the server and get the proper refusal banner.
7. UX: "Solver busy" page / sketch banner for refusals; Replay link removed from history (Sketch is the working surface); whole-kg/m³ display rounding.

## Conversation

**2026-07-22 16:19 claude-code:** Run run-20260722-1618 completed — On the morning after go-live, planners solving two dates at once hit gateway timeouts, retried, and unknowingly stacked 13 duplicate solve jobs that kept the solver busy for over an hour and slowed the whole system — even though every solve actually succeeded invisibly. We reconstructed the entire chain from logs (solver box, database, app log): solves take ~3.5 minutes but the web server gave up waiting at 5 minutes once anything queued, and abandoned requests kept a system worker tied up indefinitely.

We fixed it at every layer: a second solve for the same date is now refused instantly with a friendly message saying who is already solving it and how long it will take; at most 4 solves can run at once (matching the solver machine's 4 processors, which now genuinely run in parallel instead of one at a time); the web server now waits long enough for a normal solve to return to the browser; abandoned requests free their worker after 9 minutes; and a browser quirk that let the same person accidentally queue a duplicate from a second window was closed. Without these, the first busy morning would have re-triggered the same pile-up, with logistics losing an hour of planning time and the office seeing a slow system.

---

**2026-07-22 16:22 — you**

done
