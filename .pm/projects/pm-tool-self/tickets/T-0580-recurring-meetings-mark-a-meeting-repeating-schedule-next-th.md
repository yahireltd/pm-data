---
id: T-0580
title: "Recurring meetings: mark a meeting repeating + schedule-next that copies agenda & stakeholders (not outcomes)"
type: feature
state: in_progress
created: 2026-07-15T11:13:06Z
updated: 2026-07-15T11:13:20Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - A meeting can be marked repeating (weekly / every 2 weeks / monthly) from its header, and un-marked
  - "A repeating meeting offers 'Schedule next meeting': creates a NEW meeting one interval later copying agenda, stakeholders, reminders config and setup — but NOT outcomes, minutes, or attachments"
  - Each occurrence records its own outcomes/minutes independently
  - "The chain is navigable: the source shows a link to the next occurrence (and the new one links back); schedule-next can't create duplicates"
  - Works for org-level meetings (M-010) and project meetings alike
out_of_scope: []
code_anchors:
  - path: web/app/_actions/meetings.ts
    symbol: setMeetingRecurrence / scheduleNextOccurrence
  - path: web/app/_components/MeetingDetailHeader.tsx
    symbol: Repeats select + Schedule next
  - path: schemas/meeting.schema.json
    symbol: recurrence / follows / next
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1113
    started: 2026-07-15T11:13:20Z
    status: in_progress
labels:
  - meetings
attention: null
version: 3
---

## Problem

M-010 is a weekly global meeting, but the tool has no recurrence: each week either the same meeting gets reused (outcomes pile up across weeks, minutes blur together) or someone hand-recreates it (agenda + stakeholders re-typed). Austin's design: each occurrence is its OWN meeting with its own outcomes/minutes; a "make it repeating" control plus, at the end of a meeting, a "schedule next" button that copies the setup (agenda, stakeholders — and reminders config) but never the outcomes.

## Design

- Meeting gains `recurrence` ({unit: day|week|month, interval} | null) plus `follows`/`next` links so the series is a chain of individual meetings.
- Header UI: a "Repeats" select (none / weekly / every 2 weeks / monthly), and — when repeating — a "Schedule next meeting" button; once the next exists the button becomes a link to it (no accidental duplicates). A "follows" chip links back up the chain.
- scheduleNextOccurrence: new meeting at scheduled_at + interval; copies title, kind, duration, location, organizer, project/pre-project, milestone, stakeholders, agenda, reminder config, and the recurrence itself; fresh body/outcomes/attachments.
