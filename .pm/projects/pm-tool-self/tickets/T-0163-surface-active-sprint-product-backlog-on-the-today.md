---
id: T-0163
title: Surface active sprint + product backlog on the Today dashboard
type: feature
state: done
priority: p1
created: 2026-06-02T15:56:14Z
updated: 2026-06-02T15:58:38Z
project: pm-tool-self
section: null
parent: null
children: []
order: 34816
reporter: null
assignee: null
acceptance_criteria:
  - Today dashboard shows a Current sprint section (active sprints across active projects with goal + committed/done progress), or a calm note when none
  - Today dashboard shows a Product backlog section (open confirmed_for_release + in_review tickets, ranked, confirmed first) so open work is visible — no more false 'all clear'
  - Rows link to the project's Sprints / Backlog tabs; stakeholder view still renders
  - web tsc clean and the dashboard route renders 200
out_of_scope: []
code_anchors:
  - path: web/app/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: confirmed_for_release
source: discovered
---
# T-0163: Surface active sprint + product backlog on the Today dashboard

## Problem

The Today dashboard (web/app/page.tsx) predates the sprint/backlog feature: its buckets only surface attention-flagged tickets, the old `ready` state, the inbox, and in-flight agent runs. Our open work lives in the product backlog (state=triaged + backlog_status) and in sprints — neither of which the dashboard knows about. Result: the home view reads 'All clear' even with a full backlog of confirmed work. Surface the active sprint + product backlog so the front door reflects reality.

## Acceptance criteria

_See frontmatter._
