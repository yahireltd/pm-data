---
id: T-0552
title: /xero/post runs the console poster (detached) instead of an in-request run; UI range capped at 3 days
type: feature
state: done
created: 2026-07-13T14:31:08Z
updated: 2026-07-13T17:21:55Z
project: yasystem
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
  - "Submitting a ≤3-day range on /xero/post starts a background run: page shows 'started in background', live logs stream, and `pgrep -f xero-post/daily` shows the process server-side"
  - A range over 3 days is rejected with a clear validation message
  - Submitting while a run is in progress does NOT start a second run — warning shown, live logs of the running one displayed
  - Completion shows in the viewer when the last window's posting_run finish row lands, and the issue-digest email arrives
  - With no DB Xero token, submit is refused with a 'connect via /xero/login' message instead of a dead spawn
out_of_scope: []
code_anchors:
  - path: backend/controllers/XeroController.php:412-510
    note: actionPost (range rule + spawn) and actionRunPost (legacy fallback, page no longer calls it)
  - path: backend/views/xero/post.php
    note: form hint, spawned/already-running states, completion detection in the poller (also fix r.created → r.created_at)
  - path: console/controllers/XeroPostController.php:200-203
    note: daily-cap skip now logged to DB so the viewer can terminate
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260713-1431
    model: claude-fable-5
    started: 2026-07-13T14:31:37Z
    status: in_progress
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-13T14:31:37Z
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - xero
  - accounts
attention: null
version: 4
---

## Problem
/xero/post currently runs the whole posting inside a browser-held HTTP request (fetch to /xero/run-post): php-fpm's request_terminate_timeout can SIGKILL it mid-run, Apache buffering hides progress, a closed laptop lid strands the run, and the session-bound flow caused the historic zombie/timeout pain (T-0509/T-0520 era). The T-0534 console command (xero-post/daily) already solves all of this — mutex, circuit breaker, DB-shared token, per-run issue-digest email — but the web page doesn't use it. Austin: "change the page xero/post in the backend so it uses the console function if that is possible. (date ranges should be limited to 3 days in the UI)".

## Fix
- actionPost: on a valid submit, verify the DB token store has a token (console runner is headless; friendly "go to /xero/login" otherwise), pre-check the yh_xero_post_all mutex (warn + show live logs if a run is already going), then spawn `php yii xero-post/daily --from=.. --to=..` detached (nohup ... & — same pattern ForecastingController uses) and log a "spawned" row so the viewer shows life immediately.
- The existing live-log viewer keeps working unchanged in principle (console writes the same xero_posting_logs) — completion is detected from the posting_run finish row for the last day of the window; daily-cap aborts surface as a terminal warning (console command now logs that skip to the DB instead of stdout-only).
- Range rule: max 3 days inclusive (was 7), enforced server-side and stated in the UI.
- /xero/run-post kept as a guarded fallback but the page no longer calls it.

## Benefit
Runs survive browser closes and fpm/Apache timeouts, always finish with the issue-digest email, and can never overlap (same mutex as the cron).

## Conversation

**2026-07-13 17:21 — you**

Done and tested
