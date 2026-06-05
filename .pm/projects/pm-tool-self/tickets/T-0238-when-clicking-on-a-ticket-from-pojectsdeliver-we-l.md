---
id: T-0238
title: when clicking on a ticket from pojects/deliver we loose the menu highlight we were on
type: feature
state: done
priority: p2
created: 2026-06-05T17:01:20Z
updated: 2026-06-05T17:32:40Z
project: pm-tool-self
section: null
parent: null
children: []
order: 70656
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] when opening a ticket from a project we should not lose the menu highlight and the project menu should not colapse its difficult to navigate"
out_of_scope: []
code_anchors:
  - path: web/app/_components/Sidebar.tsx
  - path: web/app/_components/SidebarNav.tsx
  - path: web/app/_components/BackButton.tsx
  - path: web/app/tickets/[id]/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1707
    model: claude
    started: 2026-06-05T17:07:11Z
    status: completed
    summary: Opening a ticket from inside a project used to collapse the project's menu in the sidebar, so you lost your place and it was hard to navigate. Now the project you're working in stays open and highlighted (on "Deliver", where tickets live) while you view one of its tickets, and there's a Back button on the ticket to return to where you came from — the sprint or backlog list. Working inside a project now feels continuous instead of dropping you out of it.
    ended: 2026-06-05T17:29:39Z
    test_plan: 'Open a project, go to its Sprints or Backlog, and click a ticket: the sidebar should keep that project expanded with "Deliver" highlighted (not collapse), and the Back button should return you to the list you came from. Open a ticket directly in a fresh tab: Back falls back to the project (or home for an inbox ticket).'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

if i click a ticket from the sprint list it opens that ticket we need to keep the project menu open and on deliver when viewing the ticket. Also need to have a back button to go back to the last view - this should be the behaviour when working on a project  not to close that menu

## Conversation

**2026-06-05 17:29 claude:** Run run-20260605-1707 completed — Opening a ticket from inside a project used to collapse the project's menu in the sidebar, so you lost your place and it was hard to navigate. Now the project you're working in stays open and highlighted (on "Deliver", where tickets live) while you view one of its tickets, and there's a Back button on the ticket to return to where you came from — the sprint or backlog list. Working inside a project now feels continuous instead of dropping you out of it.

---

**2026-06-05 17:32 — you**

works
