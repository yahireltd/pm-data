---
id: T-0237
title: project filter on the reveiw page (persist last worked on project and filter by that by default)
type: feature
state: done
priority: p2
created: 2026-06-05T16:51:28Z
updated: 2026-06-05T21:23:48Z
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

**2026-06-05 21:23 claude:** **What we did** — Added a project filter to the Review page, and made it remember the last project you were working in so it shows that project's items by default. You can still switch to "All projects" to see everything, or pick any single project to focus on.

**Why** — The Review page showed every project's attention items all at once, even when you were focused on just one project, making it harder to find what was relevant to you.

**What would have happened if we did nothing** — People would keep scanning through a mixed list of items from every project, wasting time and risking missing the things that actually needed their attention.

**The benefit** — When you open Review, you now land directly on the project you were last in, so you see what matters to your current work right away. This helps anyone juggling multiple projects stay focused, while still letting them see everything when they need to.
