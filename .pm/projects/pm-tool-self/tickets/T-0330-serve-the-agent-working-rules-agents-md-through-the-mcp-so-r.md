---
id: T-0330
title: Serve the agent working rules (AGENTS.md) through the MCP so remote agents don't need the code checkout
type: feature
state: triaged
created: 2026-06-09T19:28:25Z
updated: 2026-06-09T19:28:25Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - An agent with ONLY the remote MCP connection (no code checkout) can retrieve the full working rules before touching any ticket
  - The served conventions and the repo AGENTS.md come from a single source of truth (no second copy to drift)
  - The dev wiki section on the MCP tool surface documents how conventions are served
  - "Bonus: pm_claim_ticket nudges agents who have not fetched the conventions (only if cheap to implement)"
out_of_scope: []
code_anchors:
  - path: AGENTS.md
  - path: mcp-server/src/server.ts
    symbol: server instructions / tool registration
  - path: web/app/docs/content.ts
    symbol: MCP tool surface section
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - mcp
  - dogfood-find
  - agent-experience
attention: null
version: 1
---

## Problem

The working rules for agents (AGENTS.md — claim before you code, Definition of Ready, close-the-loop attestation, plain-language write-ups, agent policy) live only in the code repository. An agent connecting to the live MCP from a machine WITHOUT the pm-tool checkout has no way to read them — it will use the tools while blind to the conventions the force-gates assume it knows. This came up in practice today: an agent planning a sprint for a different project (yasystem) only followed the conventions because the pm-tool repo happened to be on the same machine.

## Ideas

Pick whichever fits the architecture best:

- Include the conventions (or a distilled version) in the MCP server's instructions field so every connecting client receives them automatically.
- Expose them as an MCP resource and/or a pm_get_conventions tool.
- Return a short conventions digest inside pm_get_project (which agents already call first), alongside the agent_policy it already returns.

The conventions text should be served from one source of truth so the repo copy and the served copy cannot drift.