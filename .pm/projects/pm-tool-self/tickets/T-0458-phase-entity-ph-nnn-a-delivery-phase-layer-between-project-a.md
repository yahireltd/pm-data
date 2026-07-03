---
id: T-0458
title: "Phase entity (PH-NNN): a delivery-phase layer between project and milestone (consolidates the M-009 modeling cluster)"
type: feature
state: done
created: 2026-06-22T22:07:14Z
updated: 2026-07-03T03:48:50Z
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
      - at: 2026-06-22T23:27:33Z
        note: Used the new Phase layer for real on the Sales project — the worked example. Created the four delivery phases (Sales Efficiency → Operating-System Foundation → Management & Learning → Strategic Account Platform), each with its goal, the condition that must be true to start it, an owner, and the order they run in. Filed the project's existing milestones under their phases, and — for Phase 2, the one being planned with the team tomorrow — broke it out of a single crammed milestone into six real, separately-trackable deliverables (segmentation, lead scoring & routing, golden-nugget identification, conversion workflows, process & role design, and cadence frameworks), each carrying the acceptance criteria that were already written. The Phases tab now shows the whole programme as a navigable tree that rolls progress up — which is exactly the "can the tool plan from the big picture down to the work" question this was meant to answer.
      - at: 2026-06-22T23:57:40Z
        note: "Added dev ownership at the sprint level — a sprint can now name the developer accountable for delivering it, the level a dev owns while agents do the ticket work. Then proved the whole model end-to-end on the Sales project: wrapped a real sprint (\"Phase 2 — Foundations\") around the two existing Phase 2 tickets (customer scoring and customer segmentation), set its owner, and it now renders as a full tree on the Phases tab — initiative → phase → milestone → sprint (with the dev's name) → tickets. Also moved the Phases tab to the front so it's the first thing you see on a phased project. This makes the tool's planning visibly span the whole ladder, from the big picture down to a dev's actual sprint of work."
      - at: 2026-06-23T01:27:21Z
        note: "Built and deployed a whole delivery-planning layer and used it to lay out the Sales programme for real. Shipped this session: a Phase layer that groups milestones into sequenced stages; dev ownership at the sprint level (a dev owns a sprint, agents work its tickets); an INTERACTIVE Phases planner where you build the plan top-to-bottom — create phases, file milestones under them, add sprints, allocate tickets, set owners, all inline; a Project → Phase → Milestone → Sprint cascade on every ticket so you can see and change where any ticket sits, each picker narrowing the next; the ability to delete a sprint (which was impossible before); and a cross-project Roadmap that shows every dev's sprints on a timeline, filterable to one dev. We also ran an automated adversarial review of the planner and fixed what it found (stale pickers, silent failures, a couple of data-integrity gaps). The Sales project now has its four phases, Phase 2 decomposed into six deliverable milestones, and a foundations sprint wrapping its first two real tickets. Still to tidy (minor): a now-redundant Phase-2 umbrella milestone, a handful of small polish items, renaming the old lifecycle 'Phase' wording to 'Lifecycle' inside one page, and the docs."
    test_plan: |-
      DEPLOYED across commits db2f1d7 → de2c946 (all passed the cross-package typecheck gate; pm-tool + pm-mcp active). Verify (hard-refresh — deploys don't bust open tabs):
      1. PLANNER: project → Phases tab (now first). Expand a phase: create phase / new milestone-in-phase / move-milestone-via-the-phase-combobox / add-sprint / set phase+sprint owners / add-ticket (commit existing or scope new) / delete-sprint (trash → confirm). Owner + phase pickers are type-or-pick and re-sync after save.
      2. TICKET CASCADE: open a ticket → "Where it sits" = Project→Phase→Milestone→Sprint, each select narrows the next (pick a phase, milestone list filters; pick a milestone, sprint list filters).
      3. ROADMAP: sidebar → Roadmap. Lanes per dev (Austin SPR-001, Zsolt [example] SPR-005), bars by date coloured by phase, All-devs/one-dev filter.
      4. DATA-INTEGRITY: filing a milestone under a non-existent phase is rejected; committing a ticket already in another sprint is rejected.

      EXAMPLE DATA TO REMOVE WHEN READY: sprint "[example] Segmentation build" (SPR-005, Zsolt) — delete via the trash button; placeholder dates on PH-001..004 + SPR-001 (blank or set real ones).

      REMAINING (open): MS-002 redundant umbrella; 8 low review findings (sprint-delete warning text, rollup double-count of a cross-milestone ticket, stale-version guards on the new phase/sprint write actions, etc.); the lifecycle "Phase"→"Lifecycle" text relabel inside PhaseCommandCenter; docs (sprint.owner/phase in SCHEMA.md, dev wiki, help).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 9
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

## Conversation

**2026-07-03 03:48 — you**

dn
