---
id: T-0354
title: Work Mode — personal toggle that hides your private projects from your own view
type: feature
state: in_progress
priority: p2
created: 2026-06-10T15:08:48Z
updated: 2026-06-12T01:43:27Z
project: pm-tool-self
section: null
parent: null
children: []
order: 98304
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "A 'Work mode' toggle in the top bar, persisted per browser (cookie, so server components can filter), hides every private project you can see from all aggregate surfaces: sidebar groups, dashboard, /projects, /me, search, dev-tickets, and the pickers (promote / move-to-system / project assignment)"
  - Tickets belonging to hidden private projects disappear from cross-project lists (tickets, review, ready, in-progress, activity) while work mode is on
  - A clear always-visible indicator shows work mode is ON; one click turns it off and everything returns
  - Enforced at the visibleProjects()/visibleTickets() chokepoint (T-0348) — no per-page filtering that can drift
  - "Colleagues' visibility is UNCHANGED: private projects are already hidden from other users server-side (ADR-035/T-0290/T-0348) — work mode is a personal de-clutter/screen-share filter, not a security boundary; direct URLs to your own private projects still open"
out_of_scope:
  - Changing who can see private projects (already enforced)
  - Per-project mute/pin (this is one global personal toggle)
  - Hiding private MEETINGS/decisions outside project-scoped surfaces (follow-up if needed)
code_anchors:
  - path: web/app/_lib/access.ts
    symbol: visibleProjects / visibleTickets — the T-0348 chokepoint; add the work-mode cookie filter here
  - path: web/app/_components/TopNav.tsx
    symbol: work-mode toggle + ON indicator
  - path: web/app/_components/RoleSwitcher.tsx
    symbol: pattern for a personal view toggle
  - path: web/app/projects/[slug]/layout.tsx
    symbol: pm_last_project cookie — the cookie read/write pattern for server components
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260612-0143
    model: claude-fable-5
    started: 2026-06-12T01:43:27Z
    status: in_progress
labels: []
attention: null
version: 5
---

## Problem

Austin has private projects (e.g. stock-predictions-engine) that are correctly hidden from colleagues (ADR-035: `private: true` + invited, enforced server-side since T-0348) — but the OWNER still sees them everywhere: sidebar, dashboard, lists. That's clutter in the work view, and a screen-share risk in the office.

## Design

A personal "Work mode" toggle:

- Lives in the top bar; state in a cookie (`pm_work_mode`) — a cookie, not localStorage, because the Sidebar/dashboard are SERVER components and the filter must apply at render.
- When ON, the `visibleProjects()` / `visibleTickets()` access chokepoint additionally drops projects with `private: true` (and their tickets) for the current viewer. One filter point — every surface that already routes through it (T-0348) inherits the behaviour for free.
- Always-visible ON indicator; one click off.
- NOT a security boundary: colleagues never saw these projects anyway. Direct URLs to your own private project still work — it's your data, work mode just keeps it out of the shared-screen furniture.

Fits the T-0358 nav work (the new Systems/Initiatives/Closed groups all derive from the same visible set, so they filter for free).
