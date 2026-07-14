---
id: T-0565
title: Ticket search
type: feature
state: review
priority: p2
created: 2026-07-14T11:49:25Z
updated: 2026-07-14T12:23:15Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 118784
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - add a search for the tickets
out_of_scope: []
code_anchors:
  - path: web/app/_components/SidebarSearch.tsx
    symbol: sidebar search + / shortcut
  - path: web/app/tickets/page.tsx
    symbol: ?q= filter by id/title/labels
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260714-1222
    started: 2026-07-14T12:22:37Z
    status: completed
    ended: 2026-07-14T12:23:15Z
    summary: "Backlog audit: ticket search already exists. The sidebar has a \"Search tickets…\" box (press \"/\" anywhere to jump into it); it takes you to the Tickets page filtered by your query, matching ticket id, title, and labels. If that's what you were after, this can just close. If you meant something deeper — searching inside ticket DESCRIPTIONS and comments (full-text), or a dedicated search page — send this back with a note and that becomes the real scope."
    test_plan: 1. Click the "Search tickets…" box in the sidebar (or press "/"), type a word from a ticket title → the Tickets page filters to matches. 2. Search a ticket id (e.g. T-0423) and a label — both match. 3. Search a word that appears only in a ticket's DESCRIPTION — it will NOT match (search covers id/title/labels only). If you need that, send back with "full-text" and we'll scope it.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-14T12:23:15Z
version: 11
---

Create a search for a ticket

## Conversation

**2026-07-14 12:23 claude-code:** Run run-20260714-1222 completed — Backlog audit: ticket search already exists. The sidebar has a "Search tickets…" box (press "/" anywhere to jump into it); it takes you to the Tickets page filtered by your query, matching ticket id, title, and labels. If that's what you were after, this can just close. If you meant something deeper — searching inside ticket DESCRIPTIONS and comments (full-text), or a dedicated search page — send this back with a note and that becomes the real scope.
