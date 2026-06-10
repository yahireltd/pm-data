---
id: T-0348
title: Private project not private
type: bug
state: review
priority: p1
created: 2026-06-10T12:03:10Z
updated: 2026-06-10T12:37:25Z
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
    status: completed
    ended: 2026-06-10T12:37:25Z
    summary: "We fixed the hole where a project marked private was still visible to other admins. The privacy rule itself was correct, but most pages of the app built their lists by reading every project and every ticket directly, skipping that rule — so a private project's name, tickets, and progress leaked into the dashboard, the All tickets / Ready / In progress / Review pages, the sidebar, the counts in the top bar, the project pickers, and even the user-roles screen. If we did nothing, anything you tried to keep private (like StockPredictionsEngine) would stay effectively public to every admin. Now every one of those places lists only what the signed-in person is allowed to see, and opening a direct link to a private project's ticket shows \"not found\" unless you're invited. The benefit: private projects are actually private — you can run your own projects in the tool without other admins seeing them — while everyone you do invite sees everything exactly as before."
    test_plan: |-
      Needs a deploy first (commit e582367). Then verify as an admin who is NOT in StockPredictionsEngine's invited/team/stakeholder set — either sign in as the other admin, or use "Preview as" with that admin's email from a project page.

      Happy path (private project invisible):
      1. Dashboard (/): StockPredictionsEngine absent from every section — stat-tile counts, Needs you, Agents working, Current sprint, Upcoming meetings, Product backlog, Ready for pickup, Active projects.
      2. Top bar counts (Tickets/Review/Ready/In progress) exclude its tickets; sidebar project list and Projects count exclude it.
      3. /tickets: none of its tickets listed AND its slug missing from the Project filter chips. /queue, /watch, /review, /projects, /activity: same.
      4. Deep links: open one of its ticket URLs directly (/tickets/T-xxxx) → 404 not-found; its project URL → bounced to /me (already worked).
      5. Pickers: Cmd+K quick-add, inbox promote picker, ticket-detail project picker, meeting agenda ticket search — private project/tickets not offered.
      6. Settings → Users & roles: its name and team members not shown.

      Allowed-set regression (sign in as you / invited user):
      7. You still see StockPredictionsEngine everywhere as before (sidebar, dashboard, its tickets in lists).
      8. Non-private projects unchanged for everyone; inbox (project-less) tickets still show on /inbox and /tickets.

      Cross-impact: I touched the shared access lib (new visibleProjects/visibleTickets helpers) and 16 pages' data loading — if any page errors or renders empty for YOU (the allowed admin), that's a regression in this change. Also re-check /me and one project's Sprints tab (its committed-ticket titles still resolve).

      Known limit (not this ticket): MCP tools (pm_list_*) run as a single bearer identity, so agent-side listings aren't per-user until T-0257 (SPR-028).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-10T12:37:25Z
version: 9
---

The project StockPredictionsEngine is marked private and is visible to other admins, as are its tickets in various ready/review lists.

## Root cause

The access layer (`projectAccess` / `visibleProjectSlugs` in `web/app/_lib/access.ts`) gates private projects correctly — but only 2 of ~16 server read paths use it (activity page, ticket search). Every other page calls the raw `loadAllProjects()` / `loadAllTickets()` library functions and renders the result, so private projects and their tickets leak to any signed-in admin via the dashboard, all-tickets, ready queue, watch, review list, projects list, sidebar, inbox/meeting pickers, and direct ticket/project URLs.

## Fix shape

Centralize: add access-aware listing helpers (visible-projects / visible-tickets) next to `visibleProjectSlugs`, plus a per-page guard for detail routes (ticket detail, project layout → not-found when access is "none"), and sweep all call sites onto them.

## Out of band

The MCP server is a single bearer identity today, so MCP listings are not per-user — that is T-0257 (per-user MCP identity, SPR-028), not this ticket.

## Conversation

**2026-06-10 12:37 claude-code:** Run run-20260610-1225 completed — We fixed the hole where a project marked private was still visible to other admins. The privacy rule itself was correct, but most pages of the app built their lists by reading every project and every ticket directly, skipping that rule — so a private project's name, tickets, and progress leaked into the dashboard, the All tickets / Ready / In progress / Review pages, the sidebar, the counts in the top bar, the project pickers, and even the user-roles screen. If we did nothing, anything you tried to keep private (like StockPredictionsEngine) would stay effectively public to every admin. Now every one of those places lists only what the signed-in person is allowed to see, and opening a direct link to a private project's ticket shows "not found" unless you're invited. The benefit: private projects are actually private — you can run your own projects in the tool without other admins seeing them — while everyone you do invite sees everything exactly as before.
