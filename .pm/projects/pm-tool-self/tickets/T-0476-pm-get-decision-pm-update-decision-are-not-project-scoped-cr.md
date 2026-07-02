---
id: T-0476
title: Cross-project id ambiguity in per-project entity lookups/writes (decisions, sprints, milestones, tech-sessions, phases) → silent wrong reads + record clobbering
type: bug
state: done
created: 2026-06-23T18:58:35Z
updated: 2026-07-02T18:03:47Z
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
  - "[x] Every per-project entity get/update/set MCP tool resolves an id within a single project, never another project's: decisions (pm_get_decision/pm_update_decision), sprints (pm_get_sprint/pm_update_sprint/pm_set_sprint_owner/pm_update_sprint_state/pm_delete_sprint), milestones (pm_get_milestone/pm_update_milestone_state/pm_set_milestone_* etc.), tech-sessions (pm_get_tech_session/pm_record_tech_session), phases (pm_get_phase/pm_update_phase/pm_set_phase_owner/pm_update_phase_state)"
  - "[x] pm_get_decision and pm_update_decision gain an optional `project` param (they have NONE today — the worst case); the sprint/milestone/tech-session/phase tools already expose optional `project` but must be hardened per the next criterion"
  - "[x] For ALL these tools: when `project` is omitted AND the id exists in >=2 projects, raise an explicit ambiguity error instead of silently returning/mutating the first match (today's first-match fallback is the bug)"
  - "[x] The underlying path resolvers (findDecisionPath and the sprint/milestone/tech-session/phase equivalents in mcp-server/src/lib/paths.ts) take an optional project arg, scope to it when provided, and signal ambiguity when omitted + id matches >1 project"
  - "[x] Back-compat: an id that is unique across all projects still resolves when `project` is omitted"
  - "[x] Regression test: create the same short id (e.g. SPR-001, MS-001, ADR-001) in two projects, then assert get/update/set resolve to the correct one when `project` is supplied and error on ambiguity when omitted — for each entity family"
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
    status: completed
    progress:
      - at: 2026-07-01T14:29:32Z
        note: "Fix implemented + tested (not yet committed). Added a shared `resolveProjectScopedId(id, dirForSlug, projectSlug?)` in mcp-server/src/lib/paths.ts that collects matches across scanned projects and THROWS an explicit ambiguity error (naming the clashing projects) when the id is omitted-project and exists in >1 project — instead of silently returning the first match. Routed all five per-project finders through it: findDecisionPath (now gains an optional projectSlug — it had none, the root cause), findSprintPath, findMilestonePath, findPhasePath, findTechSessionPath. Added optional `project` to pm_get_decision + pm_update_decision schemas and threaded it in (mirrors pm_create_decision). The sprint/milestone/phase/tech-session tools already passed args.project through, so they inherit the guard with no tool-layer change. Made complete-run's TS-existence check tolerant of the new throw (a clash still counts as 'exists' for the close-the-loop gate). New regression test tests/cross-project-id.test.ts (7 tests, all green): supplied-project resolves correct record, ambiguous+no-project throws for ADR/SPR/MS, error names both projects, unique id still resolves when project omitted, missing id returns null, pm_get_decision returns the right project's ADR / refuses ambiguous. tsc --noEmit clean. NOTE: repo round-trip.test.ts fails in this checkout because it seeds from a repo-root .pm/ that isn't present here — pre-existing, unrelated to this change (baseline failed identically before my edits). NOTE: meetings (findMeetingPath, org-bucket variant) were left as-is — out of this ticket's stated scope; flag as a possible follow-up."
      - at: 2026-07-01T14:42:42Z
        note: "Folded meetings into the fix and shipped the code. Generalized the resolver into scanScopesForId (handles the org bucket via a project:null scope) + a resolveProjectScopedId wrapper; findMeetingPath now routes through it and throws on a cross-scope M- clash (project↔project or project↔org). Regression test extended to 8 cases incl. meetings (project clash throws; supplied project disambiguates; an org-only M-050 still resolves with project:null). cli+mcp typecheck clean. Committed + pushed to master as 51aab7b (T-0476 fix: project-scope per-project id lookups). Deploy pending: prod SSH to support.yahire.com is blocked in this sandbox pending Austin's approval — handing over the git-pull + restart pm-mcp step. Post-deploy test = pull a specific project's SPR/MS/ADR with the project param and confirm id-only lookups now error on ambiguity."
    test_plan: |-
      ## Verified live in production (post-deploy)
      - `pm_get_sprint {id: SPR-002, project: demo-neon-smash}` → returned demo-neon-smash's Sprint 2. ✓
      - `pm_get_milestone {id: MS-003, project: demo-neon-smash}` → returned demo-neon-smash's M3. ✓
      - `pm_get_decision {id: ADR-003, project: demo-neon-smash}` → returned demo-neon-smash's ADR-003 (new `project` param works through the live MCP without a client reconnect). ✓
      - Id-only lookups on clashing ids now ERROR with the clashing projects named:
        - `pm_get_sprint {id: SPR-001}` → "ambiguous … (pm-tool-self, demo-neon-smash, logistics-route-planning-rollout)". ✓
        - `pm_get_milestone {id: MS-001}` → ambiguous across 4 projects. ✓
        - `pm_get_decision {id: ADR-001}` → ambiguous across 5 projects (incl. target-tracker + sales-segmentation — the exact pair from the original clobber). ✓

      ## Automated
      - `cd mcp-server && bun test tests/cross-project-id.test.ts` → 8 pass (self-contained two-project fixture with colliding ADR-001/SPR-001/MS-001/M-001, incl. org-bucket meeting).
      - `cd mcp-server && bunx tsc --noEmit` → clean.
      - Note: `tests/round-trip.test.ts` fails in a code-only checkout (it seeds from a repo-root `.pm/` that isn't present); pre-existing, unrelated — run it where the seed `.pm/` exists.

      ## Reviewer checklist / cross-impact to confirm
      - Try `pm_update_decision {id, project, ...}` on a clashing ADR id → edits only that project's ADR; omitting `project` on a clashing id errors instead of clobbering (this is the bug that destroyed target-tracker's ADR-002).
      - Spot-check one write op per family (sprint/milestone/phase/tech-session) with `project` supplied.
      - `pm_complete_run`: its records-attestation gate validates the `tech_session` id via the same finder — confirm a completed run with a valid `tech_session` still closes and a bogus `TS-999` still rejects (the finder now tolerates an ambiguous TS-id as "exists" so a clash can't block a close).
      - Meetings: `pm_get_meeting` with a clashing `M-` id and no project now errors; org-bucket meetings (project: null) still resolve.

      ## Scope note
      - Only T-/P- ids are globally unique; every per-project id space is covered. Documented in SCHEMA.md (ID allocation → "Resolving per-project ids").
    ended: 2026-07-01T14:50:03Z
    summary: |-
      **What we did** — Fixed a data-integrity flaw in how the tool looks things up by their short id. Sprints, milestones, decisions (ADRs), phases, tech-sessions and meetings all number themselves starting at 1 inside each project, so the same short number exists in many projects at once. Previously, asking for one of these by number alone would quietly return whichever project's copy it found first — and editing by number alone could overwrite the wrong project's record. Now you tell it which project you mean; and if you don't, and the number exists in more than one project, it stops and says exactly which projects clash instead of guessing.

      **Why** — This had already silently returned the wrong project's decision, and once destroyed a real decision record by overwriting it. As we run more projects side by side, the clashes only get more common.

      **What would have happened if we did nothing** — People and agents would keep occasionally reading the wrong project's data (and making calls on it) and, worse, overwriting the wrong project's records — quietly corrupting the history each time.

      **The benefit** — Lookups and edits now hit exactly the record you meant, and an ambiguous request fails loudly with a clear message rather than doing the wrong thing silently. Verified live in production against real projects.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: SCHEMA.md — new 'Resolving per-project ids' subsection under ID allocation; documents that only T-/P- are global and that per-project ids need `project` to disambiguate.
labels:
  - bug
  - mcp-server
  - data-integrity
  - decisions
  - sprints
  - milestones
  - concurrency
attention: null
version: 17
defect_status: confirmed
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

**2026-07-01 14:50 claude-code:** Run run-20260701-1418 completed — **What we did** — Fixed a data-integrity flaw in how the tool looks things up by their short id. Sprints, milestones, decisions (ADRs), phases, tech-sessions and meetings all number themselves starting at 1 inside each project, so the same short number exists in many projects at once. Previously, asking for one of these by number alone would quietly return whichever project's copy it found first — and editing by number alone could overwrite the wrong project's record. Now you tell it which project you mean; and if you don't, and the number exists in more than one project, it stops and says exactly which projects clash instead of guessing.

**Why** — This had already silently returned the wrong project's decision, and once destroyed a real decision record by overwriting it. As we run more projects side by side, the clashes only get more common.

**What would have happened if we did nothing** — People and agents would keep occasionally reading the wrong project's data (and making calls on it) and, worse, overwriting the wrong project's records — quietly corrupting the history each time.

**The benefit** — Lookups and edits now hit exactly the record you meant, and an ambiguous request fails loudly with a clear message rather than doing the wrong thing silently. Verified live in production against real projects.

---

**2026-07-02 18:03 — you**

Done
