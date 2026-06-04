---
id: T-0217
title: Do not show c1 / d1 on contracts that get copied to the run planner
type: feature
state: in_progress
priority: p2
created: 2026-06-04T14:04:01Z
updated: 2026-06-04T14:05:13Z
project: logistics-route-planning-rollout
section: null
parent: null
children: []
order: 1024
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - When we copy form sketch planner to run planner - do not enter a type count if the job is not split.
  - we should not see c1 / d1 on not split jobs
out_of_scope: []
code_anchors: []
relates:
  - meeting:M-003
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-1405
    model: claude
    started: 2026-06-04T14:05:13Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
---

## Problem

_Suggested feature from meeting M-003._

Do not show c1 / d1 on contracts that get copied to the run planner
