---
id: T-0160
title: Meeting invites + reminders poller (calendar events + dedupe)
type: feature
state: done
priority: p2
created: 2026-05-29T18:13:32Z
updated: 2026-07-01T14:17:00Z
project: pm-tool-self
section: null
parent: null
children: []
order: 31744
reporter: null
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates:
  - T-0502
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: in_review
source: discovered
version: 1
---

# T-0160: Meeting invites + reminders poller (calendar events + dedupe)

## Problem

The reminders poller is unbuilt: no time-based job scans scheduled meetings to send invites/reminders N hours before with dedupe, and the meeting schema's calendar.graph_event_id / ics_url are always null (nothing creates a calendar event or sends an invite). The Microsoft Graph send pipeline is already proven (creds via AWS Secrets Manager). Consumer of the stakeholder→meeting recipient wiring. Done: poller job, ICS/calendar-event creation, invite + reminder send with dedupe, graph_event_id populated.

## Acceptance criteria

_To define at sprint promotion._
