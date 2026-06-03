---
id: T-0170
title: Comms dispatch on meeting events (meeting_scheduled / held / outcome_recorded)
type: feature
state: done
priority: p2
created: 2026-06-02T18:52:37Z
updated: 2026-06-02T00:00:00Z
project: pm-tool-self
section: null
parent: null
children: []
order: 41984
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - Creating a meeting fires meeting_scheduled; marking it held fires meeting_held; recording an outcome fires outcome_recorded
  - dispatch resolves the meeting's stakeholders (notify_on) and emails via Graph; failures never break the action
  - web tsc clean; verified via the log channel
out_of_scope: []
code_anchors:
  - path: web/app/_actions/meetings.ts
  - path: comms/src/resolve.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## Problem

_What's wrong / what's needed?_

## Context

_Background, screenshots, customer quotes._

## Design notes

_How we'll fix it._
