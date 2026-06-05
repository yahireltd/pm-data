---
id: T-0252
title: TIghten up testing
type: feature
state: in_progress
priority: p2
created: 2026-06-05T21:38:52Z
updated: 2026-06-05T21:59:16Z
project: pm-tool-self
section: null
parent: null
children: []
order: 80896
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - add the various testing criteria and.tighten up the testing parts of both tickets and projects
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2159
    model: claude
    started: 2026-06-05T21:59:16Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
---

We need to really tighten up the testing stages as we are able to move so fast. A combination of automatic testing, edge case testing / evaluation, cross checking ie if changing a shared function make sure it isnt used elsewhere eg a conroller function called in project js or ajax or a model function called by another model or controller. Also need. to make sure the user is given extensive list of things to test and check so we move forward with as little risk as possible. Maybe before starting on work identify risk
