---
id: T-0218
title: Flag for vehicles on the master branch that will allow us to ignore vehicles with that flag (do not use hire vehicles) - maybe should be date specific as they may need to use hire vehicles on a particular date
type: feature
state: review
priority: p2
created: 2026-06-04T14:04:06Z
updated: 2026-06-12T00:11:54Z
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
  - "[x] In the master branch (the current live db that gets copied over test nightly) we need to add a field  (do not use on sketch planner) that will prevent the solver using said vehicles. Or alternatively be able to mark as a hire vehicle that we can add to the pool for a particular day (when we get busy we do use hired vehicles)"
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
attention:
  needed_by: human
  reason: Implemented & committed (8b37aef5); needs the vehicle_day_pool migration deployed then human testing — steps in the Conversation.
  since: 2026-06-04T18:03:02Z
branch: SymbioticRoutePlanner
version: 1
---

## Problem

_Suggested feature from meeting M-003._

Flag for vehicles on the master branch that will allow us to ignore vehicles with that flag (do not use hire vehicles) - maybe should be date specific as they may need to use hire vehicles on a particular date

## Conversation

**2026-06-04 18:02 claude-code:** **Done** — committed `8b37aef5`.

Hire vehicles = `Vehicles.ourVehicle = 0`. They're now excluded from the sketch solver **and** the "Add Route" picker by default, unless opted into a specific date via the new per-day pool (table `vehicle_day_pool`, migration `m260604_120000` — **must be applied**). New **"Hire vehicles"** button + modal on the run planner manages the day's pool.

**To test (needs the migration deployed):**
1. On a normal sketch solve → confirm hire vehicles aren't used and don't appear in the Add Route picker.
2. On the run planner for a date → **Hire vehicles** → add one for that day → re-solve the sketch for that date → confirm it's now available/used.
3. Remove it → re-solve → confirm it's excluded again.
