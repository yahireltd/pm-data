---
id: T-0213
title: user help / docs
type: feature
state: review
priority: p2
created: 2026-06-04T02:09:55Z
updated: 2026-06-04T03:15:41Z
project: pm-tool-self
section: null
parent: null
children: []
order: 58368
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - Contextual help / tooltips on the main pages explaining what each surface + key control does (user-facing, not dev)
  - A reusable tooltip/help-hint component used across pages
  - AGENTS.md carries a rule that agents keep the user help/tooltips updated when a page or behaviour changes
out_of_scope: []
code_anchors:
  - path: web/app/_components/
    note: reusable Tooltip/HelpHint component
  - path: web/app/
    note: page-level help text
  - path: AGENTS.md
    note: keep-user-help-updated rule
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0210
    model: claude
    started: 2026-06-04T02:10:15Z
    status: completed
    summary: Reusable HelpHint tooltip + contextual help on the main pages (dashboard, tickets, projects, meetings, sprints, pre-projects). AGENTS.md §8 rule to keep help current.
    ended: 2026-06-04T03:15:41Z
    test_plan: Hover the '?' help hints on the main pages -> a tooltip explains the surface/control.
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-04T03:15:41Z
---

**add tooltips / help on all pages and also add a rule for agenta to keep documentation updated if we change it**

]

## Conversation

**2026-06-04 03:15 claude:** Run run-20260604-0210 completed — Reusable HelpHint tooltip + contextual help on the main pages (dashboard, tickets, projects, meetings, sprints, pre-projects). AGENTS.md §8 rule to keep help current.
