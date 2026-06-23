---
id: T-0476
title: pm_get_decision / pm_update_decision are not project-scoped → cross-project ADR-id collision (silent wrong reads + record clobbering)
type: bug
state: triaged
created: 2026-06-23T18:58:35Z
updated: 2026-06-23T18:58:35Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: Claude (Opus 4.8)
assignee: null
acceptance_criteria:
  - pm_get_decision accepts an optional `project` param; when set, it returns the ADR from that project and never another project's.
  - pm_update_decision accepts an optional `project` param; when set, it mutates only within that project (no cross-project write path remains).
  - findDecisionPath(id, project?) scopes resolution to the given project; when `project` is omitted AND the id exists in >=2 projects, the caller raises an explicit ambiguity error instead of silently returning the first match.
  - "Back-compat: an id that is unique across all projects still resolves when `project` is omitted."
  - "Regression test: create ADR-00X in two projects, then assert get/update resolve to the correct one when `project` is supplied and error on ambiguity when it is omitted."
  - update-decision.ts is confirmed to use the scoped resolver.
out_of_scope: []
code_anchors:
  - file: mcp-server/src/lib/paths.ts
    symbol: findDecisionPath
    lines: 311+
    note: "ROOT CAUSE: loops listProjectSlugs() and returns the FIRST `${id}-*.md` match across ALL projects. Add an optional project arg; scope to it when provided; signal ambiguity when omitted and the id matches in >1 project."
  - file: mcp-server/src/tools/get-decision.ts
    lines: 10-15
    note: Input schema is only {id}; runGetDecision calls findDecisionPath(args.id) with no project. Add optional `project` and pass it through.
  - file: mcp-server/src/tools/update-decision.ts
    note: Same handler family — apply the identical `project` param + scoped resolver; this is the path that previously clobbered another project's ADR.
  - file: mcp-server/src/tools/create-decision.ts
    lines: ~38
    note: "REFERENCE: already takes `project: z.string().min(1)` and is correctly scoped. get/update should mirror this."
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - bug
  - mcp-server
  - data-integrity
  - decisions
attention: null
version: 1
---

## Problem
ADR/decision ids are **project-scoped** — every project has its own ADR-001, ADR-002, … But the MCP decision tools `pm_get_decision` and `pm_update_decision` accept only `id` (no `project`), so they resolve an ADR id **ambiguously across ALL projects**. Result: (a) silent wrong reads — you ask for one project's ADR and get another's; and (b) destructive cross-project writes.

## Evidence
- **2026-06-23:** `pm_get_decision` for ADR-001…006 intending `sales-segmentation-account-management` (P-0018) returned the **`target-tracker-…` (P-0017)** project's ADRs — confirmed by the returned `path`.
- **Earlier:** `pm_update_decision` overwrote **target-tracker's ADR-002** with sales-segmentation content — a real clobber that had to be manually recovered.

## Root cause
`mcp-server/src/lib/paths.ts → findDecisionPath(id)` iterates `listProjectSlugs()` and returns the **first** file matching `${id}-*.md` in any project's `decisions/` dir. `get-decision.ts` (and `update-decision.ts`) call it with `args.id` only — their input schemas have no `project`. By contrast `create-decision.ts` already requires `project` and is correctly scoped (verified: creating a P-0018 ADR with `supersedes: ADR-006` left P-0017's ADR-006 untouched).

## Fix
Add an optional `project` to `pm_get_decision` and `pm_update_decision` (mirroring `pm_create_decision`) and thread it into `findDecisionPath(id, project?)`. When `project` is given, scope to it; when omitted and the id is ambiguous across projects, raise an explicit error rather than silently picking the first. Audit any other id-resolution in the decision tools.

## Why it matters
This is a cross-project **data-integrity** bug. It silently returns the wrong project's decisions (bad reads → decisions made on the wrong data) and has already destroyed a record (bad writes). It worsens as ADR numbering overlaps — which is the norm, since every project starts at ADR-001. It also blocks safe housekeeping on P-0018: ADR-002/004/005 there can't be flipped to `superseded` until this lands (see ADR-009 in that project).