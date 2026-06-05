---
id: T-0255
title: "[security] Input validation: injection & path traversal"
type: chore
state: triaged
created: 2026-06-05T21:42:38Z
updated: 2026-06-05T21:42:38Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - "backup.ts: sanitise/validate the admin name+email before they are interpolated into git -c args (or use GIT_AUTHOR_NAME/EMAIL env) — no git-arg injection"
  - All slug/id params validated with a strict regex (^[a-z0-9_-]+$ etc.) in MCP tool inputs + path builders (cli/mcp paths.ts) to block ../ path traversal
  - Inbound email header values (fromName, subject) are escaped before being embedded in ticket markdown (comms/src/inbound/parser.ts)
  - Graph token-exchange error messages no longer include the raw response body in persisted logs (comms/src/lib/graph.ts)
out_of_scope: []
code_anchors: []
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

Security review (T-0249): CRITICAL git-arg injection via the authenticated user’s name/email in backup.ts; HIGH path-traversal via unvalidated project slug in path builders (e.g. pm_create_ticket project="../../.."); MEDIUM unescaped inbound email headers into markdown + Graph error bodies written to logs.

## Context

Files: web/app/_actions/backup.ts (git -c), mcp-server/src/lib/paths.ts + tool input schemas, comms/src/inbound/parser.ts, comms/src/lib/graph.ts.