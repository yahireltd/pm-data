---
id: T-0206
title: pm_update_ticket MCP tool — edit an existing ticket's fields
type: feature
state: review
created: 2026-06-04T01:48:21Z
updated: 2026-06-04T01:56:08Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - pm_update_ticket can edit title, body, priority, type, labels, acceptance_criteria, out_of_scope, code_anchors, customer_impact, due on an existing ticket
  - Only provided fields change; `updated` bumps; the result validates against the ticket schema
  - state + id + created are NOT editable via this tool (use the state-transition tools)
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/update-ticket.ts
    note: new — mirror create-ticket.ts
  - path: mcp-server/src/server.ts
    note: register the tool
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0156
    started: 2026-06-04T01:56:08Z
    status: completed
    ended: 2026-06-04T01:56:08Z
    summary: "Added pm_update_ticket (mcp-server): edits an existing ticket's fields (title/body/priority/type/labels/acceptance_criteria/out_of_scope/code_anchors/customer_impact/due). Only provided fields change; state/id/created are immutable. Verified live by editing T-0204's title. Also refreshed a pre-existing stale tools-list test (12 -> 38 tools)."
    test_plan: Via MCP call pm_update_ticket on any ticket changing e.g. labels or title; confirm only that field changes + `updated` bumps; confirm passing `state` does NOT change state.
labels:
  - mcp
  - dx
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-04T01:56:08Z
---

## Problem
Agents can create tickets, comment, drive state, and set backlog fields — but there is NO MCP tool to EDIT an existing ticket's core fields (title, body, acceptance_criteria, priority, type, labels, code_anchors, etc.). This blocks agents from fixing/refining tickets — e.g. adding acceptance_criteria to satisfy the DoR gate, or correcting code_anchors after the fact (hit repeatedly while dogfooding).

## Ask
Add `pm_update_ticket` (mcp-server) that patches the provided editable fields on an existing ticket. Excludes `state` (use the transition tools) and `id`/`created`.

## Conversation

**2026-06-04 01:56 claude-code:** Run run-20260604-0156 completed — Added pm_update_ticket (mcp-server): edits an existing ticket's fields (title/body/priority/type/labels/acceptance_criteria/out_of_scope/code_anchors/customer_impact/due). Only provided fields change; state/id/created are immutable. Verified live by editing T-0204's title. Also refreshed a pre-existing stale tools-list test (12 -> 38 tools).
