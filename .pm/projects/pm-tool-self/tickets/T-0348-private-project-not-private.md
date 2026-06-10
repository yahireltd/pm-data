---
id: T-0348
title: Private project not private
type: bug
state: in_progress
priority: p1
created: 2026-06-10T12:03:10Z
updated: 2026-06-10T12:25:05Z
project: pm-tool-self
section: null
parent: null
children: []
order: 97280
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "A private project and ALL its data (tickets, sprints, meetings agenda items, activity) are invisible to admins not in its allowed set (invited/team/stakeholders) on every web page: dashboard, all-tickets, ready queue, watch, review list, projects list, sidebar, inbox pickers, dev-tickets, meeting pickers"
  - Opening a private project's URL directly (project pages, ticket detail) returns not-found for a non-allowed user — no leak via deep link
  - Allowed users (creator/invited/team/stakeholders) still see the private project everywhere they did before
  - Non-private projects are unaffected for everyone
out_of_scope: []
code_anchors:
  - path: web/app/_lib/access.ts
    symbol: visibleProjectSlugs — correct gate, just unused by most pages
  - path: web/app/page.tsx
    symbol: admin dashboard — loadAllTickets unfiltered
  - path: web/app/tickets/page.tsx
  - path: web/app/queue/page.tsx
  - path: web/app/watch/page.tsx
  - path: web/app/review/page.tsx
  - path: web/app/projects/page.tsx
  - path: web/app/_components/Sidebar.tsx
    symbol: admins deliberately get all projects — must exclude private
  - path: web/app/tickets/[id]/page.tsx
    symbol: no canSeeProject guard
  - path: web/app/projects/[slug]/layout.tsx
    symbol: no canSeeProject guard
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260610-1225
    model: claude-fable-5
    started: 2026-06-10T12:25:05Z
    status: in_progress
labels: []
attention: null
version: 8
---

The project StockPredictionsEngine is marked private and is visible to other admins, as are its tickets in various ready/review lists.

## Root cause

The access layer (`projectAccess` / `visibleProjectSlugs` in `web/app/_lib/access.ts`) gates private projects correctly — but only 2 of ~16 server read paths use it (activity page, ticket search). Every other page calls the raw `loadAllProjects()` / `loadAllTickets()` library functions and renders the result, so private projects and their tickets leak to any signed-in admin via the dashboard, all-tickets, ready queue, watch, review list, projects list, sidebar, inbox/meeting pickers, and direct ticket/project URLs.

## Fix shape

Centralize: add access-aware listing helpers (visible-projects / visible-tickets) next to `visibleProjectSlugs`, plus a per-page guard for detail routes (ticket detail, project layout → not-found when access is "none"), and sweep all call sites onto them.

## Out of band

The MCP server is a single bearer identity today, so MCP listings are not per-user — that is T-0257 (per-user MCP identity, SPR-028), not this ticket.
