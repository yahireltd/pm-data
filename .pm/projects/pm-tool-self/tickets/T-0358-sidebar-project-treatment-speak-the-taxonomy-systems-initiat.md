---
id: T-0358
title: Sidebar + project treatment speak the taxonomy — Systems / Initiatives / Closed groups; systems lose the initiative machinery (ADR-039 follow-up)
type: feature
state: review
created: 2026-06-11T21:39:11Z
updated: 2026-06-11T22:01:31Z
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
    status: completed
    progress:
      - at: 2026-06-11T21:44:50Z
        note: "Implementation complete: the sidebar now shows Systems / Initiatives / Closed (grouping purely by what a project is and whether it's finished — the old hidden flags no longer move things around), and opening a system hides the delivery machinery (Phase, Charter, Milestones, Time plan) in both the side menu and the top tabs, with one shared definition so they can't drift. Visiting a system's Phase page directly shows a friendly explanation instead of gates, and a system's front door lands on Sprints. Help and schema docs updated. Typecheck clean; adversarial review running before commit."
    ended: 2026-06-11T22:01:31Z
    summary: |-
      **What we did**
      Made the left menu speak the new language: projects are now grouped as Systems, Initiatives, and Closed (finished ones, shown dimmed) — grouped purely by what each project is and whether it's done, not by old hidden flags. And opening a system now feels like a system: the delivery machinery (Phase, Charter, Milestones, Time plan) disappears from its menus, its front door lands on the work itself, and visiting its Phase page directly shows a friendly explanation instead of gates and checklists.

      **Why we did it**
      The previous step gave projects a kind (system vs initiative), but the menu still used the old labels and a system still presented itself like a delivery project — phases, charters and gates it would never use. Austin asked for the toggle's meaning to actually show in the sidebar and in how projects are treated.

      **If we did nothing**
      The toggle would have been a label with no visible effect: the menu would keep its old groupings driven by invisible flags, and systems would keep wearing delivery clothes — confusing for anyone deciding where to look or what the tool was asking of them.

      **The benefit**
      The menu now tells the truth at a glance: what you run, what you're delivering, and what's finished. Systems are calmer and simpler to work in — just the work, meetings, decisions and status — while deliveries keep the full guided journey.
    test_plan: |-
      After `pm-deploy` (commit d340b54) and a hard refresh:

      **Sidebar groups**
      1. The left menu now shows SYSTEMS (Yasystem, Yahire Website), INITIATIVES (Yasite, Yahire Website Backend, the demo game, Stock Predictions, Logistics, pm-tool-self), and — only if any project is done/cancelled — a dimmed CLOSED group.
      2. pm-tool-self appears under Initiatives (the old "meta" rule is gone). If you want it under Systems, toggle its kind on the Overview — it should move instantly.
      3. Yahire Website Backend should now appear under Initiatives (its old hidden pin no longer applies). 

      **System treatment**
      4. Open Yasystem: the side menu under it shows Overview · Plan · Deliver · Collaborate · Operate, but Plan opens straight to Decisions (no Charter/Milestones/Time plan tabs), and there is no Phase entry. Top tabs match the side menu exactly.
      5. Click Yasystem's name in the sidebar → it lands on Sprints (not Charter or a phase page).
      6. Type the URL /projects/yasystem/phase directly → a friendly note ("This is a system — there are no phases to advance") with a link to the Overview, NOT a gates checklist.
      7. Type /projects/yasystem/charter directly (stale-bookmark case) → the page still renders, and in the sidebar the "Plan" group is highlighted (nav doesn't go blank).

      **Initiatives unchanged**
      8. Open Yasite (an initiative): full nav — Phase, Charter, Milestones, Time plan all present; base route still lands on the phase-appropriate tab.

      **Edge cases**
      9. Stakeholder preview (?view=stakeholder) on a system: no errors; sub-groups with nothing visible simply don't render.
      10. Toggle a project to System and back on the Overview — each switch is reflected in the sidebar after refresh, and the in-page nav trims/expands accordingly.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: SCHEMA.md, schema/type field descriptions, project-overview help — same commits.
labels:
  - taxonomy
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-11T22:01:31Z
version: 5
---

## Problem

T-0350 made kind behavioural for the dashboard and grouping LOGIC, but the sidebar still speaks the old T-0258 language ("Existing Projects" / "Projects In Development"), and opening a system project still presents the full initiative machinery — Phase tab, Charter, Milestones, Time plan — which ADR-031 explicitly flagged as a follow-up ("possible follow-up if it proves noisy"). It proved noisy.

## Design (ratified with Austin, 2026-06-11)

- Sidebar: three groups — **Systems** (kind system), **Initiatives** (kind initiative, in flight), **Closed** (done/cancelled, dimmed). Placement derives purely from kind + state; the legacy in_development pin and meta-label fallback stop affecting grouping (the Overview kind switch is the one source of truth, and it already clears stale pins).
- System treatment: nav trimmed via a single `initiativeOnly` flag on PROJECT_NAV items + `navForKind()` filter used by BOTH the sidebar sub-nav and the top tabs (one definition, can't drift). Systems keep Overview, Decisions, Sprints/Backlog/List/Board, Meetings/Tech sessions, Status.
- /phase on a system: friendly empty-state, not gates. Base route lands on Sprints.

## Conversation

**2026-06-11 22:01 claude-code:** Run run-20260611-2139 completed — **What we did**
Made the left menu speak the new language: projects are now grouped as Systems, Initiatives, and Closed (finished ones, shown dimmed) — grouped purely by what each project is and whether it's done, not by old hidden flags. And opening a system now feels like a system: the delivery machinery (Phase, Charter, Milestones, Time plan) disappears from its menus, its front door lands on the work itself, and visiting its Phase page directly shows a friendly explanation instead of gates and checklists.

**Why we did it**
The previous step gave projects a kind (system vs initiative), but the menu still used the old labels and a system still presented itself like a delivery project — phases, charters and gates it would never use. Austin asked for the toggle's meaning to actually show in the sidebar and in how projects are treated.

**If we did nothing**
The toggle would have been a label with no visible effect: the menu would keep its old groupings driven by invisible flags, and systems would keep wearing delivery clothes — confusing for anyone deciding where to look or what the tool was asking of them.

**The benefit**
The menu now tells the truth at a glance: what you run, what you're delivering, and what's finished. Systems are calmer and simpler to work in — just the work, meetings, decisions and status — while deliveries keep the full guided journey.
