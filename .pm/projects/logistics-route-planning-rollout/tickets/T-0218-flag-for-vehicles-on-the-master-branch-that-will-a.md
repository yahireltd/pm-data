---
id: T-0218
title: Flag for vehicles on the master branch that will allow us to ignore vehicles with that flag (do not use hire vehicles) - maybe should be date specific as they may need to use hire vehicles on a particular date
type: feature
state: in_progress
priority: p2
created: 2026-06-04T14:04:06Z
updated: 2026-06-04T14:09:29Z
project: logistics-route-planning-rollout
section: null
parent: null
children: []
order: 2048
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - In the master branch (the current live db that gets copied over test nightly) we need to add a field  (do not use on sketch planner) that will prevent the solver using said vehicles. Or alternatively be able to mark as a hire vehicle that we can add to the pool for a particular day (when we get busy we do use hired vehicles)
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-003
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-1409
    model: claude
    started: 2026-06-04T14:09:29Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
---

## Problem

_Suggested feature from meeting M-003._

Flag for vehicles on the master branch that will allow us to ignore vehicles with that flag (do not use hire vehicles) - maybe should be date specific as they may need to use hire vehicles on a particular date
