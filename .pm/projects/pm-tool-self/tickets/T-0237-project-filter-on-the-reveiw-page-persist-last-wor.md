---
id: T-0237
title: project filter on the reveiw page (persist last worked on project and filter by that by default)
type: feature
state: done
priority: p2
created: 2026-06-05T16:51:28Z
updated: 2026-06-05T17:27:25Z
project: pm-tool-self
section: null
parent: null
children: []
order: 69632
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] we can filter by project"
  - "[x] last worked on prokect (link clicked on the projects list) is the default sort / filter"
out_of_scope: []
code_anchors:
  - path: web/app/review/page.tsx
  - path: web/app/review/ReviewFilters.tsx
  - path: web/app/_components/RememberLastProject.tsx
  - path: web/app/projects/[slug]/layout.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1707
    model: claude
    started: 2026-06-05T17:07:32Z
    status: completed
    summary: The Review page now has a project filter, and it remembers the project you were last working in and shows that project's items by default — so you land on what's relevant to what you're working on, instead of every project's attention items at once. "All projects" switches back to seeing everything, and picking a project filters to it. The current project is remembered automatically whenever you open a project, so the default just follows where you've been.
    ended: 2026-06-05T17:23:00Z
    test_plan: "Open any project page, then go to Review: it should default to that project's attention items, with that project shown in the selector. Switch the selector to \"All projects\" to see everything; pick a different project to filter to it. The existing All/Mine/Agent-finished/Customer filter still works alongside the project filter (both stay in the URL). With no project ever opened, Review defaults to All projects."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

On the review page and anywhre else that lists out tickets we need a project filter

## Conversation

**2026-06-05 17:23 claude:** Run run-20260605-1707 completed — The Review page now has a project filter, and it remembers the project you were last working in and shows that project's items by default — so you land on what's relevant to what you're working on, instead of every project's attention items at once. "All projects" switches back to seeing everything, and picking a project filters to it. The current project is remembered automatically whenever you open a project, so the default just follows where you've been.

---

**2026-06-05 17:27 — you**

works fine
