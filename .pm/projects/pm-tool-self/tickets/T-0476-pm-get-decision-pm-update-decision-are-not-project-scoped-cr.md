---
id: T-0476
title: Cross-project id ambiguity in per-project entity lookups/writes (decisions, sprints, milestones, tech-sessions, phases) → silent wrong reads + record clobbering
type: bug
state: in_progress
created: 2026-06-23T18:58:35Z
updated: 2026-07-01T14:18:58Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: Claude (Opus 4.8)
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "Every per-project entity get/update/set MCP tool resolves an id within a single project, never another project's: decisions (pm_get_decision/pm_update_decision), sprints (pm_get_sprint/pm_update_sprint/pm_set_sprint_owner/pm_update_sprint_state/pm_delete_sprint), milestones (pm_get_milestone/pm_update_milestone_state/pm_set_milestone_* etc.), tech-sessions (pm_get_tech_session/pm_record_tech_session), phases (pm_get_phase/pm_update_phase/pm_set_phase_owner/pm_update_phase_state)"
  - pm_get_decision and pm_update_decision gain an optional `project` param (they have NONE today — the worst case); the sprint/milestone/tech-session/phase tools already expose optional `project` but must be hardened per the next criterion
  - "For ALL these tools: when `project` is omitted AND the id exists in >=2 projects, raise an explicit ambiguity error instead of silently returning/mutating the first match (today's first-match fallback is the bug)"
  - The underlying path resolvers (findDecisionPath and the sprint/milestone/tech-session/phase equivalents in mcp-server/src/lib/paths.ts) take an optional project arg, scope to it when provided, and signal ambiguity when omitted + id matches >1 project
  - "Back-compat: an id that is unique across all projects still resolves when `project` is omitted"
  - "Regression test: create the same short id (e.g. SPR-001, MS-001, ADR-001) in two projects, then assert get/update/set resolve to the correct one when `project` is supplied and error on ambiguity when omitted — for each entity family"
out_of_scope: []
code_anchors:
  - path: mcp-server/src/lib/paths.ts
    symbol: findDecisionPath
    lines: 311+
    note: "ROOT CAUSE (decisions): loops listProjectSlugs() and returns the FIRST match across ALL projects. Add optional project arg; scope when provided; error on ambiguity when omitted."
  - path: mcp-server/src/lib/paths.ts
    note: Same first-match pattern for the sprint/milestone/tech-session/phase path resolvers — audit and apply the identical scoped-resolve + ambiguity-error fix to each.
  - path: mcp-server/src/tools/get-decision.ts
    lines: 10-15
    note: Input schema is only {id}; add optional `project` and thread it through (decisions have NO project param today).
  - path: mcp-server/src/tools/update-decision.ts
    note: Same — the write path that previously clobbered another project's ADR.
  - path: mcp-server/src/tools/sprint-tools.ts
    note: get/update/set/state/delete sprint — `project` is already optional in the schema but resolution still first-matches when omitted; enforce ambiguity error.
  - path: mcp-server/src/tools/milestone-tools.ts
    note: get/state/set-owner/set-phase milestone — same optional-project hardening.
  - path: mcp-server/src/tools/tech-session-tools.ts
    note: get/record tech-session — same optional-project hardening.
  - path: mcp-server/src/tools/create-decision.ts
    lines: ~38
    note: "REFERENCE: already requires `project` and is correctly scoped; get/update should mirror it."
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260701-1418
    model: claude-opus-4-8
    started: 2026-07-01T14:18:58Z
    status: in_progress
labels:
  - bug
  - mcp-server
  - data-integrity
  - decisions
  - sprints
  - milestones
  - concurrency
attention: null
version: 5
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

## Conversation

**2026-07-01 14:17 claude-code:** **2026-07-01 — Broadened scope (Austin).** This ticket was decisions-only, but the same root cause hits every per-project id space. Per-project ids (SPR-, MS-, TS-, PH-, ADR-) all restart at 001 in each project, so the same short id exists in many projects. Looking one up by id alone can silently resolve to the wrong project's record.

- **Decisions** (`pm_get_decision` / `pm_update_decision`) are the worst case — they take **no** `project` param at all, and already clobbered a real ADR (original evidence above).
- **Sprints / milestones / tech-sessions / phases** *do* expose an **optional** `project` param, but because it's optional the id-only path still first-matches → same silent wrong-read/wrong-write when omitted.

So the fix is one consistent rule across all of them: scope resolution to `project` when given, and **error on ambiguity** (id in ≥2 projects) instead of first-match. Title, acceptance criteria and code anchors updated to cover the full entity set.

Note this is distinct from T-0316 (done), which fixed *concurrent-create duplicate ids* — a different bug. This one is *cross-project lookup ambiguity*.
