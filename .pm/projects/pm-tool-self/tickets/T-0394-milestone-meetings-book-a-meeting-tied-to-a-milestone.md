---
id: T-0394
title: "Milestone meetings: book a meeting tied to a milestone"
type: feature
state: done
created: 2026-06-16T17:16:35Z
updated: 2026-06-22T15:28:48Z
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
attention: null
version: 5
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

## Conversation

**2026-06-22 14:04 claude-code:** **Backlog triage 2026-06-22 — this looks already SHIPPED; safe to close.**

Booking a meeting tied to a milestone shipped in the same milestone-governance commits (`3e0d8cc` + `c5e0bb1`, "milestone owner/stakeholders + milestone meetings"). Recommend closing as done.

---

**2026-06-22 15:28 — you**

This is now working

---

**2026-06-22 15:28 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
