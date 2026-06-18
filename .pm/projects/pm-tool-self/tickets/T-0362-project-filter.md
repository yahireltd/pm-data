---
id: T-0362
title: project filter
type: feature
state: done
priority: p2
created: 2026-06-12T01:47:37Z
updated: 2026-06-18T16:05:33Z
project: pm-tool-self
section: null
parent: null
children: []
order: 99328
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] persistent project selector that filters on all views to that project"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260612-0148
    model: claude
    started: 2026-06-12T01:48:48Z
    status: completed
    summary: "Added a project filter so you can focus the whole tool on one project at a time. There's now a selector in the left sidebar, just under the search box: pick a project and every shared view — the dashboard, the all-tickets list, Review, Ready, In progress, the Progress feed, and the little count badges in the top bar — narrows to just that project's work. Pick \"All projects\" to go back to everything. Your choice sticks as you move around the app (it's remembered for 30 days) until you change it. It only ever hides things you could already see — it can't reveal anything you don't have access to. Before this, the only way to focus on a project was to filter each page separately, and the counts always showed everything. Benefit: one click puts the entire workspace into the context of a single project, which is much faster when you're heads-down on one thing."
    ended: 2026-06-18T13:36:54Z
    test_plan: |-
      1. As an admin with 2+ projects, look in the left sidebar just below the search box — there's a project selector showing "All projects".
      2. Pick a project. The page should refresh and the all-tickets list, dashboard tiles, Review, Ready (queue), In progress (watch), and Progress (activity) should all show only that project's tickets. The top-bar count badges (Tickets/Review/Ready/In progress) should drop to that project's numbers too.
      3. Navigate between pages (e.g. Dashboard → Tickets → Review) WITHOUT touching the selector — the filter should stay applied (it persists).
      4. Set it back to "All projects" — everything returns to the full, unfiltered view.
      5. On the All tickets page specifically: with a project focused, the page defaults to that project, but the per-page project chips still work — click another project's chip, or the "all" chip, to override just for that page; "Clear all" returns to the focused project's baseline.
      6. Edge/important: confirm it can only NARROW. The selector lists only projects you can see; a focused project never exposes anything you couldn't already access. Inbox (tickets not in any project) is intentionally left out of the project-scoped counts.
      7. Cross-impact: this reuses the shared ticket-reading helper. Sanity-check that project detail pages, sprints, and meeting ticket-links still resolve tickets correctly (those deliberately ignore the filter). Members/stakeholders (non-admins) should NOT see the selector.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 9
---

project filter below search tickets all notifications and lists are filtered to said project when selected

## Conversation

**2026-06-18 13:36 claude:** Run run-20260612-0148 completed — Added a project filter so you can focus the whole tool on one project at a time. There's now a selector in the left sidebar, just under the search box: pick a project and every shared view — the dashboard, the all-tickets list, Review, Ready, In progress, the Progress feed, and the little count badges in the top bar — narrows to just that project's work. Pick "All projects" to go back to everything. Your choice sticks as you move around the app (it's remembered for 30 days) until you change it. It only ever hides things you could already see — it can't reveal anything you don't have access to. Before this, the only way to focus on a project was to filter each page separately, and the counts always showed everything. Benefit: one click puts the entire workspace into the context of a single project, which is much faster when you're heads-down on one thing.

---

**2026-06-18 16:05 — you**

project filter tested and filters the data to the correct project
