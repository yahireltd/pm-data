---
id: T-0214
title: ❯ the agents should follow the rules to create milestones and update them
type: feature
state: review
priority: p2
created: 2026-06-04T02:30:42Z
updated: 2026-06-04T03:15:41Z
project: pm-tool-self
section: null
parent: null
children: []
order: 59392
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - AGENTS.md milestone rule (§5) updated so agents create/update milestones as part of hygiene — but CONFIRM with the user before creating or changing a milestone (not autonomous)
out_of_scope: []
code_anchors:
  - path: AGENTS.md
    note: "milestone rule: add 'ask the user to confirm first'"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0231
    model: claude
    started: 2026-06-04T02:31:24Z
    status: completed
    summary: "AGENTS.md §5: agents create/update milestones as hygiene but confirm with the user first (high-signal markers: propose, don't auto-create)."
    ended: 2026-06-04T03:15:41Z
    test_plan: Read AGENTS.md §5 -> the confirm-first milestone rule is present.
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-04T03:15:41Z
---

## Problem

_What's wrong / what's needed?_

## Context

## Design notes

## Conversation

**2026-06-04 03:15 claude:** Run run-20260604-0231 completed — AGENTS.md §5: agents create/update milestones as hygiene but confirm with the user first (high-signal markers: propose, don't auto-create).
