---
id: T-0239
title: Hard delete meetings
type: feature
state: review
priority: p2
created: 2026-06-05T17:04:49Z
updated: 2026-06-05T17:15:16Z
project: pm-tool-self
section: null
parent: null
children: []
order: 71680
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - A meeting can be deleted
out_of_scope: []
code_anchors:
  - path: web/app/_actions/meetings.ts
    symbol: deleteMeeting
  - path: web/app/_components/MeetingDetailHeader.tsx
  - path: mcp-server/src/tools/delete-meeting.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1707
    model: claude
    started: 2026-06-05T17:07:43Z
    status: completed
    summary: Admins can now permanently delete a meeting. There's a Delete button on the meeting page — admin-only, with a confirm step — and an agent tool for it too. This is for scratch or test meetings that shouldn't stick around. Like deleting a ticket, a deleted meeting is gone for good, which is why it's limited to admins and asks you to confirm first.
    ended: 2026-06-05T17:15:16Z
    test_plan: As an admin, open a meeting and click Delete, then confirm — the meeting is removed and you're returned to the meetings list. As a non-admin, there should be no Delete control. Over the agent tools, pm_delete_meeting removes a meeting by id (pass the project for a project meeting; omit it for an org-wide or pre-project meeting).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T17:15:16Z
---

Admins need to have a way to hard delete meetings.

## Conversation

**2026-06-05 17:15 claude:** Run run-20260605-1707 completed — Admins can now permanently delete a meeting. There's a Delete button on the meeting page — admin-only, with a confirm step — and an agent tool for it too. This is for scratch or test meetings that shouldn't stick around. Like deleting a ticket, a deleted meeting is gone for good, which is why it's limited to admins and asks you to confirm first.
