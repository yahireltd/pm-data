---
id: T-0171
title: Push meetings to calendar via Microsoft Graph (create event + invites)
type: feature
state: done
priority: p2
created: 2026-06-02T18:59:20Z
updated: 2026-06-02T00:00:00Z
project: pm-tool-self
section: null
parent: null
children: []
order: 43008
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - pushToCalendar creates a real Graph calendar event with the meeting's email stakeholders as attendees
  - The returned event id is stored in meeting.calendar.graph_event_id; payload-building is unit-verifiable
  - Graph token + from-address reused from the comms config; web tsc clean
out_of_scope: []
code_anchors:
  - path: comms/src/lib/calendar.ts
  - path: web/app/_actions/meetings.ts
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
