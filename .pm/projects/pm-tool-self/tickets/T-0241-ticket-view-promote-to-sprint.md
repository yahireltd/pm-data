---
id: T-0241
title: Ticket view promote to sprint
type: feature
state: done
priority: p2
created: 2026-06-05T17:13:27Z
updated: 2026-06-05T21:24:09Z
project: pm-tool-self
section: null
parent: null
children: []
order: 73728
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] we can add a ticket to a sprint on the ticket view if we have a project selected"
out_of_scope: []
code_anchors:
  - path: web/app/_components/AddToSprintControl.tsx
  - path: web/app/tickets/[id]/page.tsx
  - path: web/app/_actions/sprints.ts
    symbol: commitTicket
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1714
    model: claude
    started: 2026-06-05T17:14:47Z
    status: completed
    summary: From a ticket that belongs to a project, you can now add it to one of that project's open sprints right there on the ticket page — a small "Add to sprint" picker in the sidebar lists the project's planned and active sprints. Before, you had to go to the project's Backlog/Sprints screen to pull work in. Adding still requires the ticket to have acceptance criteria (so half-formed work can't slip into a sprint); if it doesn't, you're told inline.
    ended: 2026-06-05T19:11:47Z
    test_plan: "Open a ticket that has a project: a \"Sprint\" field in the sidebar shows an \"Add to sprint…\" picker listing the project's planned/active sprints. Pick one — the ticket joins that sprint's committed list (verify on the Sprints tab). A ticket with no acceptance criteria shows the inline \"add success criteria first\" error. A ticket with no project shows no picker. Closed/cancelled sprints don't appear."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

When project exists we should be able to promote to any unclosed sprint

## Conversation

**2026-06-05 19:11 claude:** Run run-20260605-1714 completed — From a ticket that belongs to a project, you can now add it to one of that project's open sprints right there on the ticket page — a small "Add to sprint" picker in the sidebar lists the project's planned and active sprints. Before, you had to go to the project's Backlog/Sprints screen to pull work in. Adding still requires the ticket to have acceptance criteria (so half-formed work can't slip into a sprint); if it doesn't, you're told inline.

---

**2026-06-05 19:15 — you**

verified it works

**2026-06-05 21:24 claude:** **What we did** — You can now add a ticket to one of its project's open sprints directly from the ticket page, using a small picker in the sidebar. There's no need to switch over to the project's separate planning screen first.

**Why** — Previously, the only way to pull a piece of work into a sprint was to leave the ticket and go to the project's planning view, which added friction to a very common action.

**What would have happened if we did nothing** — People would keep bouncing between screens every time they wanted to schedule a ticket, making sprint planning slower and easier to skip.

**The benefit** — Scheduling work is faster and happens right where you're already looking. As a safeguard, a ticket still needs clear success criteria before it can be added, so unfinished or vague work can't quietly slip into a sprint. This helps anyone planning or organizing the team's work.
