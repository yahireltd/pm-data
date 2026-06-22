---
id: T-0458
title: "Phase entity (PH-NNN): a delivery-phase layer between project and milestone (consolidates the M-009 modeling cluster)"
type: feature
state: in_progress
created: 2026-06-22T22:07:14Z
updated: 2026-06-22T23:07:26Z
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
      - at: 2026-06-22T23:07:26Z
        note: "Built and deployed the second half — the visual side. Every project now has a \"Phases\" tab showing a tree: each phase, the milestones filed under it, the sprints planned from those milestones, and the tickets — with a running \"X of Y tickets done\" count at every level, plus an \"owner / entry-gate / depends-on\" line per phase and a section listing any milestones not yet placed in a phase. To stop the word \"phase\" meaning two different things, the old tab that shows a project's intake→ship lifecycle stage is now labelled \"Lifecycle\", leaving \"Phases\" for the new delivery grouping. The whole feature (data, the agent tools, and this view) is now live on the server."
    test_plan: |-
      DEPLOYED: commits db2f1d7 (data + MCP) and e1d4adf (web view + Lifecycle relabel + docs) are live; pm-tool + pm-mcp both restarted active; the cross-package typecheck gate passed both deploys.

      TO SEE THE VIEW (web): open any project → the new "Phases" tab (e.g. /projects/sales-segmentation-account-management/phases). HARD-REFRESH the browser (Cmd/Ctrl+Shift+R) — a deploy doesn't refresh already-open tabs. With no phases created yet it shows "No phases yet" plus the project's milestones under "Milestones not yet in a phase". Confirm the old lifecycle tab now reads "Lifecycle" and that opening /phases does NOT also highlight the Lifecycle tab (the phase/phases prefix-collision fix).

      TO USE THE TOOLS (agent): the MCP client must RECONNECT (restart Claude Code) before pm_create_phase etc. appear — they were registered at this deploy. Then: create PH-01..PH-04 on the Sales project, file MS-001..004 under them with pm_set_milestone_phase, and refresh the Phases tab — the tree should populate and the per-phase counts roll up.

      STILL TO DO (follow-up, not yet built): CLI `pm new phase`; the deeper relabel of the lifecycle "Phase" wording inside the command-center page (only the tab is relabelled so far); fuller docs (dev wiki + help). The worked example (Sales phases) needs the reconnect above.
labels: []
attention: null
version: 5
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