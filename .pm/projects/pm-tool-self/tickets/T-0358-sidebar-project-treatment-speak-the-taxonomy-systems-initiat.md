---
id: T-0358
title: Sidebar + project treatment speak the taxonomy — Systems / Initiatives / Closed groups; systems lose the initiative machinery (ADR-039 follow-up)
type: feature
state: in_progress
created: 2026-06-11T21:39:11Z
updated: 2026-06-11T21:44:50Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Sidebar groups are Systems / Initiatives / Closed (done+cancelled, dimmed), derived purely from kind + state — legacy in_development and the meta label no longer affect placement
  - System projects hide Phase, Charter, Milestones and Time plan in BOTH the sidebar sub-nav and the top tabs; Decisions, Deliver views, Meetings/Tech sessions and Status remain
  - Visiting a system's /phase URL directly shows a friendly note (not gates), pointing at the Overview kind switch
  - A system's base project route lands on Sprints, never on a phase-derived hidden tab
  - Help (overview/dashboard/dev-tickets), SCHEMA.md and schema/type descriptions updated to the new group names in the same change
out_of_scope:
  - Linter status-cadence exemption for systems (deferred in ADR-039)
  - Hiding charter/milestones/time-plan routes themselves (nav-hidden is enough; only /phase gets a guard)
  - Surfaces and ticket-level branch (T-0351 / T-0352)
code_anchors:
  - path: web/app/_lib/project-nav.ts
    symbol: ProjectNavItem gains initiativeOnly; navForKind() helper; defaultSection for systems
  - path: web/app/_components/Sidebar.tsx
    symbol: 3-way grouping (systems/initiatives/closed) replaces devSlugs
  - path: web/app/_components/SidebarNav.tsx
    symbol: renders three groups; ProjectSubNav filters by kind
  - path: web/app/_components/ProjectPhaseTabs.tsx
    symbol: kind prop → navForKind filter
  - path: web/app/projects/[slug]/layout.tsx
    symbol: pass kind to ProjectPhaseTabs
  - path: web/app/projects/[slug]/phase/page.tsx
    symbol: system guard with friendly note
  - path: web/app/projects/[slug]/page.tsx
    symbol: base redirect → sprints for systems
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260611-2139
    model: claude-fable-5
    started: 2026-06-11T21:39:30Z
    status: in_progress
    progress:
      - at: 2026-06-11T21:44:50Z
        note: "Implementation complete: the sidebar now shows Systems / Initiatives / Closed (grouping purely by what a project is and whether it's finished — the old hidden flags no longer move things around), and opening a system hides the delivery machinery (Phase, Charter, Milestones, Time plan) in both the side menu and the top tabs, with one shared definition so they can't drift. Visiting a system's Phase page directly shows a friendly explanation instead of gates, and a system's front door lands on Sprints. Help and schema docs updated. Typecheck clean; adversarial review running before commit."
labels:
  - taxonomy
attention: null
version: 4
---

## Problem

T-0350 made kind behavioural for the dashboard and grouping LOGIC, but the sidebar still speaks the old T-0258 language ("Existing Projects" / "Projects In Development"), and opening a system project still presents the full initiative machinery — Phase tab, Charter, Milestones, Time plan — which ADR-031 explicitly flagged as a follow-up ("possible follow-up if it proves noisy"). It proved noisy.

## Design (ratified with Austin, 2026-06-11)

- Sidebar: three groups — **Systems** (kind system), **Initiatives** (kind initiative, in flight), **Closed** (done/cancelled, dimmed). Placement derives purely from kind + state; the legacy in_development pin and meta-label fallback stop affecting grouping (the Overview kind switch is the one source of truth, and it already clears stale pins).
- System treatment: nav trimmed via a single `initiativeOnly` flag on PROJECT_NAV items + `navForKind()` filter used by BOTH the sidebar sub-nav and the top tabs (one definition, can't drift). Systems keep Overview, Decisions, Sprints/Backlog/List/Board, Meetings/Tech sessions, Status.
- /phase on a system: friendly empty-state, not gates. Base route lands on Sprints.
