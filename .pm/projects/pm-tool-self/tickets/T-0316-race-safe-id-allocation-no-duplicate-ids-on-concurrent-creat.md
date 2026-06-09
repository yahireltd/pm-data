---
id: T-0316
title: Race-safe id allocation (no duplicate ids on concurrent creates)
type: bug
state: triaged
created: 2026-06-09T16:51:21Z
updated: 2026-06-09T16:51:21Z
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
  - Concurrent creates (web / CLI / MCP, same or different process) never allocate the same id; the counter read-increment-write is atomic (file lock or atomic rename)
  - "Covers every id space: T- (global) and the per-project M- / MS- / ADR- / SPR- / TS- / PP- / SN- counters"
  - "Verified by a concurrent-create test: fire N creates at once -> N distinct ids, no lost increments"
out_of_scope: []
code_anchors:
  - path: cli/src/lib
  - path: mcp-server/src/tools/create-ticket.ts
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

Ids come from a counter (read -> increment -> write). Two creates landing together can read the same value and both write `counter+1`, producing a duplicate id (or a silently lost increment). The cross-project id-collision class is real — it has bitten us in practice.

## Approach

Make the counter increment atomic in the shared allocator — a lockfile, an atomic `O_EXCL` create + rename, or `flock`. Small and independent of the version-check work, so it can ship first.

## Context

The "b" of the a+b approach (T-0270 / SPR-029). Exact allocator location confirmed by the spike (T-0315).