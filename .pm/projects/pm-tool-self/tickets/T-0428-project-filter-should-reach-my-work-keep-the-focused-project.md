---
id: T-0428
title: Project filter should reach "My work" + keep the focused project expanded in the nav
type: bug
state: review
created: 2026-06-18T16:41:42Z
updated: 2026-06-18T16:42:21Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Austin
  channel: web
  contact: austin@yahire.com
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - /me ('My work') honors the sidebar project filter — 'My tickets' shows only the focused project's tickets when a filter is set.
  - /me shows it's filtered to the selected project, with an easy 'show all' escape.
  - When a project is focused via the filter, the sidebar keeps that project expanded everywhere (not only on its own routes).
out_of_scope: []
code_anchors:
  - path: web/app/me/page.tsx
    note: /me scopes My tickets to activeProjectFilter + Filtered-to/show-all indicator
  - path: web/app/_components/SidebarNav.tsx
    symbol: filteredProject
    note: keep focused project expanded everywhere; auto-open Closed group
  - path: web/app/_components/Sidebar.tsx
    note: passes the filter cookie to SidebarNav
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260618-1642
    model: claude-opus-4-8
    started: 2026-06-18T16:42:09Z
    status: completed
    ended: 2026-06-18T16:42:21Z
    summary: "Made the project filter actually reach two places it was missing. When you focus the app on one project (the filter in the left sidebar), the \"My work\" page now shows only that project's tickets instead of everything — and it tells you it's filtered (\"Filtered to <project>\"), with a one-click \"show all\" if you want the full picture. Separately, the sidebar now keeps the focused project expanded wherever you are, so the project you're concentrating on stays open in the navigation rather than collapsing the moment you leave its pages. Before this, \"My work\" ignored the filter (it listed tickets from every project even when you'd narrowed to one), which was confusing, and the focused project would fold shut in the sidebar. Benefit: focusing on a project now behaves consistently across the whole app, including your personal work view and the nav."
    test_plan: |-
      1. In the left sidebar, pick a project in the filter selector (under the search box).
      2. Go to "My work" (/me). "My tickets" should show ONLY that project's tickets, with a line reading "Filtered to <project> · show all".
      3. Click "show all" — the list shows tickets from all your projects, and the line switches to "Showing all projects · focus <project>". Clicking that re-applies the filter.
      4. Focus a project you have no tickets in — "My tickets" shows the "Nothing on your plate" empty state (not other projects' tickets).
      5. With a project focused, look at the sidebar: that project stays EXPANDED (its sub-nav: Overview / Plan / Deliver / …) on every page, not just on its own routes. If the focused project is a Closed one, the Closed group auto-opens.
      6. Clear the filter (All projects) — /me returns to all your tickets and the sidebar stops force-expanding.
      Cross-impact: non-admins don't get the filter selector, so this only affects admins; the rest of /me (projects list, display settings) is unchanged.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ui
  - dogfood
  - for-austin
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-18T16:42:21Z
version: 5
branch: facelift/rbac-look
---

## Problem

Follow-up to T-0362 (persistent project filter). Two surfaces didn't honor it:

- **/me ("My work")** ignored the filter — "My tickets" showed every project even when the sidebar was filtered to one (see screenshot from dogfooding).
- The **sidebar nav** only expanded the project you were routed into; a project focused via the filter wasn't kept expanded.

## Fix

- /me reads `activeProjectFilter()` and scopes "My tickets" to it, with a "Filtered to <project> · show all" indicator (`?all=1` escapes).
- SidebarNav gains a `filteredProject` trigger so the focused project stays expanded everywhere (and auto-opens the Closed group if it's a closed project).

## Conversation

**2026-06-18 16:42 claude-code:** Run run-20260618-1642 completed — Made the project filter actually reach two places it was missing. When you focus the app on one project (the filter in the left sidebar), the "My work" page now shows only that project's tickets instead of everything — and it tells you it's filtered ("Filtered to <project>"), with a one-click "show all" if you want the full picture. Separately, the sidebar now keeps the focused project expanded wherever you are, so the project you're concentrating on stays open in the navigation rather than collapsing the moment you leave its pages. Before this, "My work" ignored the filter (it listed tickets from every project even when you'd narrowed to one), which was confusing, and the focused project would fold shut in the sidebar. Benefit: focusing on a project now behaves consistently across the whole app, including your personal work view and the nav.
