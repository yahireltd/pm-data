---
id: T-0173
title: "Meeting scheduling: free/busy clash check for internal attendees (Graph getSchedule)"
type: feature
state: done
priority: p2
created: 2026-06-03T12:06:19Z
updated: 2026-06-03T00:00:00Z
project: pm-tool-self
section: null
parent: null
children: []
order: 45056
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - A 'Check availability' action returns free/busy for a meeting's internal (same-domain) email attendees over its time window
  - Uses Graph getSchedule with the working app creds; external attendees are reported as unknown, not errors
  - Surfaced on the meeting page as a clash warning; web tsc clean
out_of_scope: []
code_anchors:
  - path: comms/src/lib/calendar.ts
  - path: web/app/_actions/meetings.ts
  - path: web/app/_components/MeetingDetailHeader.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## Problem

_What's wrong / what's needed?_

## Context

_Background, screenshots, customer quotes._

## Design notes

_How we'll fix it._
