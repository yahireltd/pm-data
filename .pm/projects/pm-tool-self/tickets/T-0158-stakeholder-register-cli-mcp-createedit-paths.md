---
id: T-0158
title: Stakeholder register — CLI + MCP create/edit paths
type: feature
state: done
priority: p2
created: 2026-05-29T18:13:32Z
updated: 2026-06-02T00:00:00Z
project: pm-tool-self
section: null
parent: null
children: []
order: 29696
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - pm stakeholder add|list|edit|remove works over project.stakeholders[], reusing shared validation
  - MCP exposes add/edit/remove stakeholder tools at parity with the web actions
  - Validation (channel, contact-by-channel, notify_on) is shared (cli/src/lib/stakeholders.ts); web re-exports it
  - cli + web + mcp tsc clean; lint 0 errors
out_of_scope: []
code_anchors:
  - path: cli/src/lib/stakeholders.ts
  - path: cli/src/commands/stakeholder.ts
  - path: cli/src/index.ts
  - path: web/app/_lib/stakeholders.ts
  - path: mcp-server/src/tools/stakeholder-tools.ts
  - path: mcp-server/src/server.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: in_review
source: discovered
---

# T-0158: Stakeholder register — CLI + MCP create/edit paths

## Problem

The project-level stakeholder register exists today only in the web UI (Overview tab → Add stakeholder; addStakeholder/updateStakeholder/removeStakeholder server actions). There is NO `pm stakeholder` / `pm new stakeholder` CLI command and NO MCP tool, so agents and scripts can't manage stakeholders — the same agent-path gap class as the meeting-kind / sprint MCP gaps. Add CLI + MCP create/edit/remove paths over project.stakeholders[] (reuse _lib/stakeholders helpers). Done: pm CLI command, MCP tool(s), parity with the web actions, lint clean.

## Acceptance criteria

_To define at sprint promotion._
