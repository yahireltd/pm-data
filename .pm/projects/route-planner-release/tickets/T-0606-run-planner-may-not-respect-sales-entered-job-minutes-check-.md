---
id: T-0606
title: Run planner may not respect sales-entered job minutes — check split and non-split paths (Nana, UAT meeting)
type: bug
state: triaged
created: 2026-07-16T17:28:13Z
updated: 2026-07-16T17:28:13Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Nana (via Austin, M-001 UAT meeting)
  channel: meeting
assignee: null
acceptance_criteria:
  - "Reproduction documented: what sales enters vs what the run planner shows, for one non-split and one split contract"
  - Root cause identified (incl. whether T-0379's assignment bug is the cause)
  - Fix applied so non-split jobs always show sales-entered minutes; split behaviour agreed with logistics and implemented
  - Nana confirms the reported case now shows the right minutes
out_of_scope: []
code_anchors:
  - path: common/models/YaRuns.php
    note: getLoadUnLoadMinutes — known `=` vs `==` assignment bug (T-0379)
  - path: backend/views/logistics/run-planner.php
    note: job minutes display
  - path: common/services/SketchPlanService.php
    note: finalise writes run contracts incl. jobMins
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - route-planner
  - release-blocker
attention: null
version: 1
---

## Problem
From M-001 (16 Jul UAT meeting) outcomes: "Nana mentioned the original run planner not respecting the job minutes entered by sales — check this isn't only when it is split and even if it is when split is this the right behaviour". This outcome was recorded but had no follow-up ticket — filing it so it doesn't get lost before Monday's release.

## What to establish
1. Reproduce: does a job's sales-entered minutes (jobMins / duration on the contract) carry through to the run planner row for a NON-split job?
2. For a SPLIT job: what happens to the minutes on each piece — copied verbatim (duplicating time), divided, or dropped to a default? The solver splits service time across pieces (service_special_piece_sec etc.) — check the run planner path does something consistent.
3. Decide the right behaviour for split pieces with logistics and implement.

## Context
Likely related to the known getLoadUnLoadMinutes assignment bug (T-0379, `=` vs `==`) which silently overwrites minutes. Check that first — it may be the whole story.