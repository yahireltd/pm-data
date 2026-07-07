---
id: T-0521
title: "Error-log floods: RBAC blocks /signal/* click beacons (2k+ 403s/day); driver offline pages 404 on Jquery.min.js case mismatch"
type: bug
state: triaged
created: 2026-07-07T14:42:10Z
updated: 2026-07-07T14:42:10Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: log survey during T-0520 investigation
assignee: null
acceptance_criteria:
  - No ForbiddenHttpException entries for signal routes from normal users after deploy
  - Driver offline pages load jquery successfully on the Linux server
  - Error/404 log rotation retention extends beyond hours
out_of_scope: []
code_anchors:
  - path: backend/config/main.php
    symbol: AccessControl allowActions
  - path: backend/views/layouts/main.php
    symbol: signal/click beacon ~:914-932
  - path: backend/views/drivers/deliver-contract-offline.php
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - logging
  - rbac
  - drivers
attention: null
version: 1
---

## Problem
Log survey (2026-07-07) found the backend error/404 logs churning their entire 5x10MB retention within hours — which is why 5 June evidence for T-0520 was unrecoverable. Two causes, both self-inflicted:

1. **403 flood (~2,100/day)**: the RBAC telemetry click-beacon (`/signal/click`, fired from `backend/views/layouts/main.php` on EVERY click by EVERY logged-in user, shipped with the 24 Apr RBAC commit) is not in mdmsoft AccessControl `allowActions`. Any user whose role lacks the signal routes generates ForbiddenHttpException + $_GET context dump per click. Confirmed across many users/IPs (normal office traffic, not one stuck tab).
2. **404 flood (~2,700/day) + functional bug**: `backend/web/js/Jquery.min.js` has a capital J on disk; the three driver OFFLINE views (`deliver-contract-offline`, `collect-contract-offline`, `collect-split-contract-offline`) reference lowercase `/js/jquery.min.js`. Case-insensitive dev Macs resolve it; the Linux server 404s → jQuery does not load at all on the offline driver pages (drivers' devices generate the 404s all day).

## Fix
- `backend/config/main.php`: added `signal/*` to AccessControl allowActions (with comment).
- `git mv Jquery.min.js jquery.min.js` (no references to the capital spelling exist).

## Test plan
1. Deploy; click around the backend as a NON-admin user → no new ForbiddenHttpException rows in error.log for signal/click.
2. Load a driver offline page (deliver-contract-offline) on a real device → no 404 for jquery.min.js in 404.log, page scripts work.
3. After a day, check error.log/404.log rotation has stopped churning (mtimes on .1–.5 stay old).
4. Cross-impact: RBAC still denies signal admin VIEWS (rbac panel) to non-admins — only the beacon endpoints are opened; verify /rbac/* still 403s for a normal user.