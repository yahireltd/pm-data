---
id: T-0206
title: pm_update_ticket MCP tool — edit an existing ticket's fields
type: feature
state: triaged
created: 2026-06-04T01:48:21Z
updated: 2026-06-04T01:48:21Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
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
agent_runs: []
labels:
  - mcp
  - dx
attention: null
---

## Problem
Agents can create tickets, comment, drive state, and set backlog fields — but there is NO MCP tool to EDIT an existing ticket's core fields (title, body, acceptance_criteria, priority, type, labels, code_anchors, etc.). This blocks agents from fixing/refining tickets — e.g. adding acceptance_criteria to satisfy the DoR gate, or correcting code_anchors after the fact (hit repeatedly while dogfooding).

## Ask
Add `pm_update_ticket` (mcp-server) that patches the provided editable fields on an existing ticket. Excludes `state` (use the transition tools) and `id`/`created`.