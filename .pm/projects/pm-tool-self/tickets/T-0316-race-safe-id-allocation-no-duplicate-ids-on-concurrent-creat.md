---
id: T-0316
title: Race-safe id allocation (no duplicate ids on concurrent creates)
type: bug
state: in_progress
created: 2026-06-09T16:51:21Z
updated: 2026-06-09T17:40:37Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Concurrent creates (web / CLI / MCP, same or different process) never allocate the same id; the counter read-increment-write is atomic (file lock or atomic rename)
  - "Covers every id space: T- (global) and the per-project M- / MS- / ADR- / SPR- / TS- / PP- / SN- counters"
  - "Verified by a concurrent-create test: fire N creates at once -> N distinct ids, no lost increments"
out_of_scope: []
code_anchors:
  - path: cli/src/lib/ids.ts
    symbol: allocateId
  - path: cli/src/lib/ids.ts
    symbol: allocateMeetingId
  - path: mcp-server/src/lib/ids.ts
    symbol: nextTicketId
  - path: cli/src/lib/io.ts
    symbol: atomicWrite
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260609-1710
    model: claude-opus-4-8
    started: 2026-06-09T17:10:24Z
    status: in_progress
    progress:
      - at: 2026-06-09T17:13:06Z
        note: "Reading the actual code refined the scope. The per-project id allocation is MORE scattered than the spike showed: it's reimplemented inside each MCP create tool (e.g. create-decision.ts scans the decisions dir itself), alongside the cli pure functions (ids.ts) and the two global allocators — which are even inconsistent (cli allocateId does max(counter, scan)+1; mcp nextTicketId does counter+1, no scan). So the robust fix is one cross-process file lock + a unified counter-based allocator that every create routes through. Realistic effort ~2-3 days (a touch above the spike's 2), and it's the live write path so it ships staged + tested. Sequence: (1) a lock helper + the GLOBAL allocator (tickets/projects — highest volume, self-contained) first, tested + deployed; (2) unify the per-project allocation and route the create tools through it."
      - at: 2026-06-09T17:31:59Z
        note: "Global half SHIPPED + verified. Wrapped the global id allocators (T-/P-/S-) in a cross-process file lock — added withLock (O_EXCL lockfile, stale-steal after 10s, sync) to both the cli and mcp-server io libs, and wrapped allocateId (cli) + nextTicketId/nextProjectId (mcp), keyed on config.yml.lock so web + CLI + MCP all serialize. Tested: 8 processes x 50 concurrent allocations -> 400 distinct ids, no losses, no stuck lock. Deployed (ed7607b); pm-tool + pm-mcp healthy. Remaining (the per-project half): M-/ADR-/MS-/SPR-/TS-/SN- ids are scan-based and reimplemented inside each create tool. Cleanest fix: a shared allocateProjectId(space, slug) using the same lock + a seeded counter (scan-max fallback, so no migration), routed through ~15-20 create sites across cli/mcp/web; ~1-1.5 days, careful write-path work, deployed once when all sites are routed + tested."
      - at: 2026-06-09T17:40:37Z
        note: "Foundation committed (44bb3e3) + the full routing mapped. allocateProjectId is proven (400-concurrent test). Remaining = route ~25 call sites onto it (each entity must move across ALL its surfaces together, or they race each other): CLI — new.ts (decision L342, meeting L451, milestone L512, status-note L564), new-sprint L47, new-tech-session L47, new-incident (incident L52, handover L107); WEB _actions — decisions L148, meetings L220, milestones L91, status L135, sprints L114, tech-sessions L70, incidents L123, handover L140, pre-projects L147; MCP create tools — create-decision, create-meeting, create-status-note, milestone-tools, sprint-tools, tech-session-tools, pre-project-tools (each has its own readdirSync scan). Pattern per swap: replace the scan-based allocator with allocateProjectId(space, scope, dir, prefix, pad). Note: meetings + pre-projects have an org-level (project=null) variant -> scope='org' + the org dir. Mechanical but voluminous, on the write path -> route + smoke-test, deploy once. Decisions is the simplest first (always per-project)."
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