---
id: T-0224
title: In progress page should show project
type: feature
state: done
priority: p2
created: 2026-06-04T14:25:13Z
updated: 2026-06-05T21:23:47Z
project: pm-tool-self
section: null
parent: null
children: []
order: 66560
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] On the In progress page, the project should be shown with the ticket"
  - "[x] review page should also show the project"
out_of_scope: []
code_anchors:
  - path: web/app/_components/TicketRow.tsx
  - path: web/app/watch/page.tsx
  - path: web/app/review/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-1425
    model: claude
    started: 2026-06-04T14:25:49Z
    status: completed
    summary: The In-progress page and the Review page now show which project each ticket belongs to. Both pages list work from every project together, so without the project labelled it was hard to tell what came from where — now each row carries its project name.
    ended: 2026-06-05T16:57:34Z
    test_plan: Open the In-progress page and the Review page while tickets from more than one project are present — each row should show its project name (next to the assignee/state). On a single-project setup it still reads fine.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

We should show the project on the progress page as contains things from different pojects

## Conversation

**2026-06-05 16:57 claude:** Run run-20260604-1425 completed — The In-progress page and the Review page now show which project each ticket belongs to. Both pages list work from every project together, so without the project labelled it was hard to tell what came from where — now each row carries its project name.

---

**2026-06-05 17:03 — you**

done as expected

**2026-06-05 21:23 claude:** **What we did** — The In-progress page and the Review page now clearly show which project each piece of work belongs to, with a project label on every row.

**Why** — These pages mix work from all projects together, so when several projects were active it was hard to tell at a glance where any given item came from.

**What would have happened if we did nothing** — People would have kept guessing which project each item belonged to, wasting time and risking confusion or mistakes when reviewing or tracking progress.

**The benefit** — Anyone scanning these pages can now instantly see the project behind each item, making it faster and easier to follow work across multiple projects. This helps everyone reviewing or managing progress.
