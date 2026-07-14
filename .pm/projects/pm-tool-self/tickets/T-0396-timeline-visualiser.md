---
id: T-0396
title: Timeline visualiser
type: feature
state: review
priority: p2
created: 2026-06-17T10:56:51Z
updated: 2026-07-14T12:22:56Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 108544
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - A visual timeline shows work laid out against dates
  - Reachable globally and per project
out_of_scope: []
code_anchors:
  - path: web/app/_components/Roadmap.tsx
    symbol: sprint bars by date, owner lanes, month ticks
  - path: web/app/roadmap/page.tsx
  - path: web/app/projects/[slug]/roadmap/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260714-1222
    started: 2026-07-14T12:22:36Z
    status: completed
    ended: 2026-07-14T12:22:56Z
    summary: "Backlog audit: this was already built — the Roadmap IS the timeline visualiser. It draws sprints as bars positioned by their start/end dates, in lanes grouped by owner, coloured by phase, with month ticks along the axis — available globally (Roadmap in the nav) and per project (the project's Roadmap tab). It shipped as part of the cross-project roadmap work (T-0458/T-0398). Nothing to build; please confirm it covers what you meant by \"timeline visualiser\" and close — if you wanted a different visualisation (e.g. milestones/tickets on a timeline rather than sprints), send back with that note."
    test_plan: "1. Open Roadmap from the main nav: sprints appear as horizontal bars against a month axis, laned by owner, coloured by phase. 2. Open a project → Roadmap tab: same view scoped to that project. 3. Confirm bars match the sprints' start/end dates. Close if this is the visualiser you wanted; send back if you meant a different granularity (milestones/tickets)."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-14T12:22:56Z
version: 6
---

A way to visualise what we are working on and when. A visual timeline so we can see where we are at

## Conversation

**2026-07-14 12:22 claude-code:** Run run-20260714-1222 completed — Backlog audit: this was already built — the Roadmap IS the timeline visualiser. It draws sprints as bars positioned by their start/end dates, in lanes grouped by owner, coloured by phase, with month ticks along the axis — available globally (Roadmap in the nav) and per project (the project's Roadmap tab). It shipped as part of the cross-project roadmap work (T-0458/T-0398). Nothing to build; please confirm it covers what you meant by "timeline visualiser" and close — if you wanted a different visualisation (e.g. milestones/tickets on a timeline rather than sprints), send back with that note.
