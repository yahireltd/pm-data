---
id: T-0394
title: "Milestone meetings: book a meeting tied to a milestone"
type: feature
state: triaged
created: 2026-06-16T17:16:35Z
updated: 2026-06-16T18:07:49Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - m-009
  - milestones
  - meetings
  - sales
attention:
  needed_by: human
  reason: 'Built + pushed (3e0d8cc): meetings can be tied to a milestone — a "Milestone (optional)" picker in the new-meeting dialog, a milestone pill on meeting rows, and each milestone card lists its meetings with a "Book meeting" deep-link. Web + schema + types done; MCP pm_create_meeting milestone param is the remaining slice. Needs the server deploy unblocked to show live.'
  since: 2026-06-16T18:07:49Z
version: 2
---

## Problem
Meetings attach to a project or a pre-project, never to a **milestone**. For the Sales initiative we want to book a meeting against a specific milestone — a gate / phase review — and see it from the milestone.

## Proposal
- Let a meeting reference a `milestone` (add the field to the meeting schema + a `milestone` param on `pm_create_meeting`).
- A "book meeting" affordance from the milestone view.
- The milestone lists its meetings.

## Context
M-009 / Sales demo — milestone-level review meetings are part of the phased governance story.

## Acceptance criteria
- A meeting can be scoped to a milestone (in addition to its project).
- A meeting is bookable directly from a milestone.
- The milestone shows its associated meeting(s).