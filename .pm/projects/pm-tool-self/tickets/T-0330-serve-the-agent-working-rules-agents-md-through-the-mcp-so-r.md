---
id: T-0330
title: Serve the agent working rules (AGENTS.md) through the MCP so remote agents don't need the code checkout
type: feature
state: ready
created: 2026-06-09T19:28:25Z
updated: 2026-06-22T16:16:34Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee:
  kind: agent
  name: claude
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
version: 7
collaborators:
  - kind: human
    name: austin
  - kind: human
    name: Austin Pickering
---

## Problem

The working rules for agents (AGENTS.md — claim before you code, Definition of Ready, close-the-loop attestation, plain-language write-ups, agent policy) live only in the code repository. An agent connecting to the live MCP from a machine WITHOUT the pm-tool checkout has no way to read them — it will use the tools while blind to the conventions the force-gates assume it knows. This came up in practice today: an agent planning a sprint for a different project (yasystem) only followed the conventions because the pm-tool repo happened to be on the same machine.

## Ideas

Pick whichever fits the architecture best:

- Include the conventions (or a distilled version) in the MCP server's instructions field so every connecting client receives them automatically.
- Expose them as an MCP resource and/or a pm_get_conventions tool.
- Return a short conventions digest inside pm_get_project (which agents already call first), alongside the agent_policy it already returns.

The conventions text should be served from one source of truth so the repo copy and the served copy cannot drift.

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep check, re-confirmed) — genuinely unbuilt; small, self-contained.**

The MCP server serves nothing from AGENTS.md today: no `instructions` field, no conventions tool or resource, and AGENTS.md is never read by any code path (the only "conventions" text anywhere is a hand-typed mirror in the web docs page — which is itself the drift this ticket warns about). Cleanest build: read AGENTS.md **once on disk** at server startup and expose it two ways from that single read — the MCP `instructions` field (every client gets it automatically) **+** a `pm://conventions` resource. Heads-up: the claim-time "nudge" bonus (AC4) is hard because the server is stateless per request — keep it a static one-line pointer or skip. And the MCP server needs a **restart** to pick this up (a deploy won't hot-load it). No dependency on the other four — orderable anywhere.
