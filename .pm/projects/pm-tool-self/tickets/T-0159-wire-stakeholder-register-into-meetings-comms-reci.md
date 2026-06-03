---
id: T-0159
title: Wire stakeholder register into meetings + comms recipient resolution
type: feature
state: done
priority: p2
created: 2026-05-29T18:13:32Z
updated: 2026-06-02T00:00:00Z
project: pm-tool-self
section: null
parent: null
children: []
order: 30720
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - A meeting can manage its own stakeholders in the UI (StakeholderList scope=meeting + add/edit/remove meeting actions)
  - A quick-pick pulls stakeholders from the project register onto the meeting (channel+contact carried over)
  - Surfaced on the meeting detail page; web tsc clean; lint 0 errors
  - Job 2 (dispatch on meeting events) split to T-0170
out_of_scope: []
code_anchors:
  - path: web/app/_actions/meetings.ts
  - path: web/app/_components/StakeholderList.tsx
  - path: web/app/_components/MeetingStakeholders.tsx
  - path: web/app/projects/[slug]/meetings/[id]/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: in_review
source: discovered
---

# T-0159: Wire stakeholder register into meetings + comms recipient resolution

## Problem

The project stakeholder register isn't connected to meetings or comms. Meetings carry a stakeholders[] array (and show a count) but the meeting UI can't add stakeholders or pull them from the project register, and the comms pipeline has no recipient resolution from the register. Wire: (1) pull/select project stakeholders onto a meeting; (2) resolve invite/notify recipients from the register (channel+contact already validated). Feeds the meeting-invite poller. Done: meeting UI stakeholder picker, recipient resolution, surfaced on the meeting + project surfaces.

## Acceptance criteria

_To define at sprint promotion._

## Audit note (2026-06-02)

**Audited — conflates two jobs (split when pulled).** (1) Meeting stakeholder UI/actions: extend StakeholderList to `meeting` scope + add meeting mutations + a project-register quick-pick. (2) Comms dispatch: invoke dispatch(event) on meeting create/held/outcome (meeting_scheduled / meeting_held / outcome_recorded). The comms resolver already exists.
