---
id: T-0458
title: "Phase entity (PH-NNN): a delivery-phase layer between project and milestone (consolidates the M-009 modeling cluster)"
type: feature
state: in_progress
created: 2026-06-22T22:07:14Z
updated: 2026-06-22T22:42:11Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - A first-class Phase entity PH-NNN exists per project/initiative — id/slug/title/order/goal/owner/stakeholders/target-window/state (mirrors the milestone state machine) + entry_gate + depends_on[PH-NNN] — race-safe id allocation and version stamping like other entities.
  - Milestones gain an optional `phase` parent (MS → PH); a Phase rolls up progress derived from its milestones → their sprints → tickets.
  - CLI + MCP parity to create/list/get/update a phase, set its state, and set a milestone's phase — mirroring the existing milestone tools.
  - "Web: a Phase → Milestone → Sprint → Ticket rollup view so a phased initiative (e.g. Sales P-0018) is navigable without the grouping living in milestone titles."
  - T-0391 is reframed/absorbed (ordered+gated = order+entry_gate+depends_on); T-0388/T-0390/T-0392 remain complementary, noted on each.
  - "Worked example committed: P-0018 Phase 2 re-modelled as PH-02 owning its deliverables as milestones, with one deliverable drilled to ticket level."
out_of_scope:
  - Programme layer above project (T-0390) and the initiative→system structural link (T-0388) — complementary, separate tickets.
  - A throwaway prototype — this is the real entity, built to plan on properly.
  - Decision-cycle (T-0322) and non-code-deliverable (T-0392) modelling beyond using existing primitives in the worked example.
code_anchors:
  - path: schemas/phase.schema.json
    note: NEW — mirror milestone.schema.json
  - path: schemas/milestone.schema.json
    symbol: add `phase` (PH-NNN) parent field
  - path: cli/src/types.ts
    symbol: Phase type + Milestone.phase
  - path: cli/src/lib/ids.ts
    symbol: PH- allocation via config counters (mirror MS-)
  - path: cli/src/commands
    symbol: pm phase (mirror milestone command)
  - path: mcp-server/src/tools
    symbol: pm_create/list/get/update_phase + update_phase_state + set milestone.phase
  - path: cli/src/lib/state-machine.ts
    symbol: phase states (mirror milestone)
  - path: web/app
    symbol: Phase rollup view + nav
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260622-2218
    model: claude-opus-4-8
    started: 2026-06-22T22:18:33Z
    status: in_progress
    progress:
      - at: 2026-06-22T22:42:11Z
        note: "Built the first half of the new Phase layer — the data model and the agent (MCP) tools. You can now group a project's milestones under a named Phase (e.g. \"Phase 2 — Sales OS Foundation\"), give each phase a goal, an owner, an entry gate (what must be true to start it) and an order, and the tool can roll a phase's progress up from its milestones → sprints → tickets. This is the structure that was missing: until now \"phases\" only existed as words typed into milestone titles, so a phase that needed several milestones had nowhere to live and couldn't be tracked. Still to come: the visual phase view in the web app, the relabel of the existing lifecycle \"Phase\" to \"Lifecycle\" so the word isn't used twice, and the docs. Work is on a feature branch and not yet deployed, so it won't appear live until we push and restart the server."
    test_plan: |-
      Branch: t0458-phase-entity (ticket resolved branch was master; cut a feature branch and recording that here per the branch-on-ticket convention). Not pushed yet.

      LOCAL VALIDATION DONE: cli, mcp-server and linter all typecheck clean (bunx tsc --noEmit, exit 0); the mcp tools-list guard test passes with a refreshed 65-tool snapshot (also re-synced pre-existing tools that had drifted out of it). Remaining suite failures are pre-existing + environmental — the cli/mcp fixtures copy a `.pm` data seed that isn't present in this code-only checkout (code/data split), so they ENOENT identically on master.

      TO VERIFY ONCE DEPLOYED (push → pm-deploy → restart pm-mcp → reconnect MCP client; new tools need a reconnect to appear):
      1. pm_create_phase on a test project → returns PH-001; pm_list_phases shows it with goal/state/milestone-count.
      2. pm_create_milestone with phase_id=PH-001 (or pm_set_milestone_phase on an existing one) → pm_get_phase PH-001 lists that milestone under it.
      3. pm_update_phase (title/goal/target_date/entry_gate/depends_on) and pm_update_phase_state (planned→in_progress→hit) round-trip and persist.
      4. pm_set_phase_owner / pm_add_phase_stakeholder / pm_remove_phase_stakeholder behave like the milestone equivalents (same validation).
      5. CROSS-IMPACT — milestone tools: pm_create_milestone now accepts an OPTIONAL phase_id; confirm creating a milestone WITHOUT it still works and existing milestones are untouched. pm_list_milestones/pm_get_milestone unchanged.
      6. Schema: a phase doc validates against schemas/phase.schema.json; a milestone with phase_id still validates.

      NOT YET BUILT (next): CLI `pm new phase`; web rollup view (Phase→Milestone→Sprint→Ticket tree) + the lifecycle-"Phase"→"Lifecycle" relabel; docs (SCHEMA.md, dev wiki, help).
labels: []
attention: null
version: 4
---

## Problem

A big transformation (e.g. the Sales initiative P-0018) is delivered in **phases**, but the tool has no phase layer. Today "Phase = Milestone" by convention — the grouping is jammed into milestone *titles* ("Phase 2 — Sales OS Foundation", "Chapter 3 — …"), and the real `phase` field is the *lifecycle* enum (build/test/ship), which doesn't fit. The result: a phase that needs several milestones (Sales Phase 2 = MS-002 crams **6 deliverables** — segmentation, scoring, golden-nugget, workflows, process/role design, cadence frameworks — into one milestone's criteria) has nowhere to live, so you get lost and can't roll up progress by phase.

## What we're building (decided 2026-06-22, this design session)

A **first-class Phase entity `PH-NNN`** sitting **between project/initiative and milestone**. Deliberately lightweight — mirrors the milestone state machine, no new gates of its own.

Hierarchy becomes: `Initiative → Phase (PH) → Milestone (MS) → Sprint (SPR) → Ticket (T)`. Nothing below milestone changes — sprints/tickets keep laddering up via `milestone`.

**Phase fields:** id / slug / title / order (the sequence Phase 1→N) · goal (one-line outcome) · owner + stakeholders (per-phase ownership) · target window (coarse start/end) · `entry_gate` (what must be true to start it) · `depends_on: [PH-NNN]` (phase sequencing) · state (planned|in_progress|hit|missed|cancelled). Plus one new field elsewhere: **`milestone.phase → PH-NNN`** (parent link). Progress is **derived** (rolled up) from child milestones → their sprints → tickets.

## How it consolidates the M-009 cluster

- **T-0391** (ordered, gated release slices) → **absorbed**: its "ordered + gated + status" becomes the Phase's `order` + `entry_gate` + `depends_on`. Reframe T-0391 into this.
- **T-0388** (initiative→system) → complementary: a Phase lives under the initiative; graduating a cleared milestone to BAU hangs off a Phase's milestones.
- **T-0390** (programme rollup) → the layer *above* project — orthogonal. Full stack: `Programme → Initiative → Phase → Milestone → Sprint → Ticket`.
- **T-0392** (non-code deliverables) → a Phase's process/role-design milestone *is* a non-code deliverable; they meet here.

## Build order (MVP-first, so the Sales plan is real for tomorrow)

1. **Data + agent layer (MVP):** phase.schema.json, Phase type, PH- id allocation (config counters), `milestone.phase` link, CLI + MCP create/list/get/update/state + set-milestone-phase. Mirror the existing Milestone implementation end-to-end. → lets us create PH-02 and hang MS-002's deliverables under it via MCP immediately.
2. **Web rollup:** the Phase → Milestone → Sprint → Ticket tree view + nav, so a phased initiative is navigable.
3. **Docs:** SCHEMA.md + dev wiki + help.

## Worked example (the dogfood target)

P-0018 Sales Phase 2 (MS-002) re-modelled as **PH-02** owning ~5 milestones (Segmentation · Lead scoring & routing · Conversion workflows · Process & role design [non-code] · Cadence frameworks), each delivered through sprints → tickets. Drill "Lead scoring & routing" to ticket level as the depth exemplar.