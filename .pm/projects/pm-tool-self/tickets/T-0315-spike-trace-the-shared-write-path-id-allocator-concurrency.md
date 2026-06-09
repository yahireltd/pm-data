---
id: T-0315
title: "Spike: trace the shared write path + id allocator (concurrency)"
type: spike
state: ready
created: 2026-06-09T16:50:45Z
updated: 2026-06-09T16:59:53Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - Determine whether all entity mutations funnel through ONE shared read-modify-write function, or each surface (web server actions / CLI / MCP tools) writes files independently
  - Locate the id allocator and how creates read/increment/write the counter across all id spaces (T- global, plus per-project M-/MS-/ADR-/SPR-/TS-)
  - "Output: a short write-path + id-allocator map, a firmed a+b estimate, and any scope tweaks for the atomic-id and version-check tickets"
out_of_scope: []
code_anchors:
  - path: cli/src/lib
  - path: mcp-server/src/tools/create-ticket.ts
  - path: web/app/_actions/tickets.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - concurrency
attention: null
---

## Problem

Before building the concurrency fixes (atomic id allocation + optimistic version checks), confirm the write architecture — it's the biggest swing factor on effort. Do all entity mutations funnel through ONE shared read-modify-write function (then the fixes land in a single place), or does each surface — web server actions, CLI, MCP tools — do its own file I/O (then the work multiplies ~3x)? ADR-025 implies a shared core, but verify.

## Output

A short map of the write path + the id allocator, a firmed a+b estimate, and any scope adjustments to the atomic-id and version-check tickets.

## Context

Decomposed from T-0270 (concurrent user editing); this sprint takes the "a+b — make it safe" approach (race-safe ids + optimistic version checks), not real-time collaborative editing.