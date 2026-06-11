---
id: T-0350
title: "Project kind: system vs initiative — make the split behavioural (ADR-039)"
type: feature
state: review
created: 2026-06-10T14:59:23Z
updated: 2026-06-11T21:41:26Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Project schema + types carry kind (initiative|system); existing data defaults correctly without manual edits (category:system → system)"
  - Dashboard phase-health sections (Projects that need you / Ready to close) skip system-kind projects entirely
  - Sidebar Existing vs In Development grouping derives from kind (in_development honoured as legacy override, not required)
  - "[x] Kind is editable from the project Overview and settable on create/promote"
  - "[x] Dev Tickets view and kind agree — one notion of 'system', not two parallel flags"
out_of_scope:
  - Surfaces (separate ticket)
  - Ticket-level branch (separate ticket)
  - Per-branch agent policy (deferred per ADR-039)
code_anchors:
  - path: schemas/project.schema.json
  - path: cli/src/types.ts
    symbol: Project
  - path: web/app/page.tsx
    symbol: projectStats / projectsNeedingYou — phase health currently runs for every active project
  - path: web/app/_components/Sidebar.tsx
    symbol: devSlugs — in_development||meta grouping
  - path: web/app/_components/ProjectStageControl.tsx
  - path: web/app/dev-tickets/page.tsx
    symbol: category===system filter
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260611-2050
    model: claude-fable-5
    started: 2026-06-11T20:50:04Z
    status: completed
    progress:
      - at: 2026-06-11T21:05:08Z
        note: "Implementation complete: projects now carry an explicit kind (initiative vs system) with one shared derivation for older data, so every screen agrees on what counts as a system. The dashboard's guided-lifecycle chasing now skips systems; the sidebar groups by kind (with the old manual flag honoured); the Overview gets an Initiative/System switch (replacing the old stage toggle, now with conflict-safe saves); new/promoted/converted projects are stamped initiative and Dev-Tickets sites are stamped system. Schema validated against legacy data shapes; web+MCP typechecks pass. Adversarial review running before commit."
    ended: 2026-06-11T21:24:40Z
    summary: |-
      **What we did**
      Gave every project an explicit kind: an "initiative" (a delivery with a start and an end) or a "system" (something long-lived you run and keep up, like a website or the ERP). The split now changes behaviour: the dashboard only chases initiatives through their phases — systems are left in peace; the sidebar groups projects by what they are; and each project's Overview has an Initiative/System switch. Older projects need no editing — the tool works out the right kind from the markers they already carry, using one shared rule so every screen agrees.

      **Why we did it**
      Two unlike things were sharing one shape. Long-lived systems were being nagged forever ("this project is stuck at intake!") for phases they'll never finish, and the old markers were cosmetic and inconsistent — one screen used one flag, another screen used a different one, so "is this a system?" had two different answers depending on where you looked.

      **If we did nothing**
      The dashboard's "needs you" list would stay polluted with systems that can never be unblocked, making the real signals easy to miss; and the two parallel flags would keep drifting apart as new screens picked whichever one was handy.

      **The benefit**
      The dashboard's attention lists become trustworthy — everything on them is genuinely actionable. The sidebar reflects reality (deliveries in flight vs things you run). And this is the foundation the rest of the sprint builds on: surfaces (T-0351) and ticket-level branches (T-0352) both hang off the system kind.
    test_plan: |-
      After the next `pm-deploy` (commit 33a6988) and a hard refresh:

      **Expect a one-time nav regroup** — this is intended: active projects that are deliveries now sit under "Projects In Development"; systems and finished projects under "Existing Projects".

      **Overview switch**
      1. Open any project → Overview → "Project kind" (where the old Existing/In-development toggle was). Switch a test project to System → the sidebar moves it to "Existing Projects", it disappears from /projects and the dashboard rollups, and it appears on /dev-tickets once it has open work. Switch back → reverses.
      2. **The stale-pin repair (the review catch):** open yasite-backend → Overview. It shows "Initiative" highlighted but sits under "Existing Projects" (an old pin). Click the already-highlighted "Initiative" — this should now SAVE (not no-op), and the project should move under "Projects In Development".

      **Dashboard exemption**
      3. On the dashboard, "Projects that need you" and "Ready to close" must contain NO system projects (pm-tool-self, Yasystem, the Yahire sites…). Before this change a stuck system could sit there forever.

      **One notion of system**
      4. /dev-tickets lists exactly the system projects; /projects lists none of them; the Inbox "Move to Dev Tickets" picker and a ticket page's "Move to Dev Tickets" dialog offer the same set.
      5. Create a brand-new site via Move to Dev Tickets → "+ Add new site" — the new project must appear on /dev-tickets and NOT on /projects (it's created as kind system now, without the legacy flag).

      **Create paths**
      6. "Start a new project" (Intake dialog) → new project appears under "Projects In Development" and on the dashboard (it's an initiative).
      7. Promote an inbox ticket to a project → same.

      **Cross-impact**
      8. The dashboard "Current sprint" and "Active projects" sections still render (they derive from the same filter that changed).
      9. /help → Dashboard and Dev Tickets pages describe the new behaviour; a project Overview's help describes the kind switch.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: SCHEMA.md, schema descriptions, project-overview/dashboard/dev-tickets help — same commit. ADR-039 body updated with the shipped state + ADR-031 relationship (left 'proposed' for Austin to accept).
labels:
  - taxonomy
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-11T21:24:40Z
version: 8
---

## Problem

Two unlike things share the "project" shape: delivery initiatives (need planning, phases, gates, an end) and long-lived systems under upkeep (the ERP, the websites — never end). Today's markers are cosmetic: `category:"system"` only feeds the Dev Tickets view, and `in_development` only moves a sidebar group. Systems get phase-gate nagging they shouldn't ("Projects that need you" treats an upkeep system stuck at intake as blocked forever); initiatives can quietly dodge the lifecycle by looking like upkeep.

## Design (ADR-039)

Add an explicit `kind: "initiative" | "system"` to the project schema (defaulting sensibly from today's data: `category:"system"` → system, else initiative).

Behaviour:
- Guided lifecycle (phase health, force-gates, "Projects that need you", Ready to close) applies to **initiatives only**; systems are exempt.
- Sidebar grouping derives from kind: systems → "Existing Projects", initiatives → "Projects In Development". `in_development` becomes a derived/legacy override, no longer the source of truth.
- Dev Tickets view keeps listing `category:"system"`-style projects; reconcile that with kind so there's one notion, not two.
- Kind is editable on the project Overview (like the existing stage control), and settable at creation/promote time.

## Out of band

Surfaces and ticket-level branches are separate tickets (this one is just the type split).

## Conversation

**2026-06-11 21:24 claude-code:** Run run-20260611-2050 completed — **What we did**
Gave every project an explicit kind: an "initiative" (a delivery with a start and an end) or a "system" (something long-lived you run and keep up, like a website or the ERP). The split now changes behaviour: the dashboard only chases initiatives through their phases — systems are left in peace; the sidebar groups projects by what they are; and each project's Overview has an Initiative/System switch. Older projects need no editing — the tool works out the right kind from the markers they already carry, using one shared rule so every screen agrees.

**Why we did it**
Two unlike things were sharing one shape. Long-lived systems were being nagged forever ("this project is stuck at intake!") for phases they'll never finish, and the old markers were cosmetic and inconsistent — one screen used one flag, another screen used a different one, so "is this a system?" had two different answers depending on where you looked.

**If we did nothing**
The dashboard's "needs you" list would stay polluted with systems that can never be unblocked, making the real signals easy to miss; and the two parallel flags would keep drifting apart as new screens picked whichever one was handy.

**The benefit**
The dashboard's attention lists become trustworthy — everything on them is genuinely actionable. The sidebar reflects reality (deliveries in flight vs things you run). And this is the foundation the rest of the sprint builds on: surfaces (T-0351) and ticket-level branches (T-0352) both hang off the system kind.
