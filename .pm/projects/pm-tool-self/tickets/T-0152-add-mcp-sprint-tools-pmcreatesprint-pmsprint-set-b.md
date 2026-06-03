---
id: T-0152
title: Add MCP sprint tools (pm_create_sprint / pm_sprint / set backlog status)
type: feature
state: done
priority: p2
created: 2026-05-29T17:57:12Z
updated: 2026-06-02T15:30:40Z
project: pm-tool-self
section: null
parent: null
children: []
order: 23552
reporter: null
assignee: null
acceptance_criteria:
  - MCP server exposes sprint tools - pm_create_sprint, pm_list_sprints, pm_get_sprint, pm_update_sprint_state, pm_commit_ticket, pm_uncommit_ticket
  - Tools enforce CLI-parity invariants - commit requires acceptance criteria; start requires non-empty committed_items; complete requires a non-negative velocity_actual
  - Tools are registered in mcp-server/src/server.ts and mcp-server tsc is clean
  - A sprint created via MCP validates against sprint.schema.json (pm-lint clean)
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/sprint-tools.ts
  - path: mcp-server/src/server.ts
  - path: mcp-server/src/lib/paths.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: confirmed_for_release
source: discovered
---

# T-0152: Add MCP sprint tools (pm_create_sprint / pm_sprint / set backlog status)

## Problem

Discovered this session: the Sprint entity has CLI + web but no MCP tools, so the agent path can't create/drive sprints or set backlog_status — same gap class as the meeting-kind MCP bug. Add pm_create_sprint, pm_sprint (start/complete/commit/...), and a set-backlog-fields tool.

## Acceptance criteria

_To define at sprint promotion._
