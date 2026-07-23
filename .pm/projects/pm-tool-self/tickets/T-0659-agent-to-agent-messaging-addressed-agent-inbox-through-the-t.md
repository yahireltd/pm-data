---
id: T-0659
title: "Agent-to-agent messaging: addressed agent inbox through the tool (pm_send_agent_message / pm_list_agent_messages)"
type: feature
state: triaged
created: 2026-07-23T23:27:14Z
updated: 2026-07-23T23:27:14Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
  channel: terminal
  contact: austin@yahire.com
assignee: null
acceptance_criteria:
  - An agent session can send a short message addressed to a named agent/principal via an MCP tool, without a human relaying it
  - A receiving agent can list its unread messages (filtered to its own principal/agent name) and mark them read/consumed
  - The MCP server's handshake instructions tell agents to check their inbox at session start, so delivery doesn't depend on the human remembering
  - Messages are attributable (sender principal from the bearer token) and ephemeral or TTL'd like file handoffs — they are coordination traffic, not records; anything decision-worthy still belongs on tickets/ADRs
  - Works across machines (e.g. Mac session ↔ Linux session) using only the remote MCP server
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/handoff-tools.ts
    symbol: storeHandoff
  - path: mcp-server/src/server.ts
    symbol: buildServer
  - path: mcp-server/src/http.ts
    symbol: resolvePrincipal
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem

Two agent sessions coordinating on the same task (today: the Mac session preparing a benchmark pack, the Linux session receiving it via the new T-0658 file handoff) have no way to message each other through the tool. Every coordination step routes through Austin verbally relaying between terminals. Async channels exist — ticket Conversation comments, handoff `note` fields, attention flags — but none are *addressed to an agent*: nothing tells the other session "there is a message for you", so nothing is read unless a human prompts it.

## Idea (Austin, 2026-07-24)

Build the missing primitive on the pattern T-0658 just established (S3-backed, bearer-authed, attributable, TTL'd):

- `pm_send_agent_message {to, body, regarding?}` — `to` is an agent name or token principal; `regarding` optionally links a ticket/handoff id.
- `pm_list_agent_messages {unread_only?}` — filtered to the caller's principal; marks-read on read or via an explicit ack.
- Delivery hook: the server's handshake `instructions` (AGENTS.md) already reach every connecting agent — add "check your inbox at session start / before claiming work". That makes delivery reliable without polling loops or push.

## Scope notes

- Messages are ephemeral coordination traffic (TTL like handoffs), NOT the record — decisions still go to tickets/ADRs/comments. Keep them out of the data repo.
- Attribution falls out of the existing per-user token → principal mapping (ADR-037).
- Out of scope: real-time push/streaming; human inboxes (humans have the web UI + email).