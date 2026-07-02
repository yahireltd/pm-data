---
id: T-0373
title: A way to see meetings that were already held
type: feature
state: review
priority: p2
created: 2026-06-15T14:51:32Z
updated: 2026-07-02T12:43:20Z
project: pm-tool-self
section: null
parent: null
children: []
order: 106496
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Held (past) meetings remain visible in the meetings view rather than disappearing once held
  - Past meetings are separated from upcoming ones (e.g. a 'previous meetings' section or filter)
  - A past meeting can be opened from that view to see its details/minutes
out_of_scope: []
code_anchors:
  - path: web/app/_components/MeetingsList.tsx
    symbol: Upcoming/Previous split + renderRows
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1243
    started: 2026-07-02T12:43:08Z
    status: completed
    ended: 2026-07-02T12:43:20Z
    summary: "You can now easily find meetings that already happened. Before, every meeting sat in one long list sorted by date, so once a meeting was marked held it got mixed in and buried — people said it felt like held meetings \"disappeared.\" The meetings view now has two clear sections: \"Upcoming\" (meetings still scheduled, next one first) and \"Previous\" (meetings that were held or cancelled, most recent first). The Previous section only appears when there are past meetings, and clicking any past meeting still opens it to read its details and minutes. This applies everywhere meetings are listed — inside a project, the org-wide meetings page, and pre-projects. If we'd left it, finding what was decided in a past meeting would stay a scrolling hunt. Benefit: past meetings are one glance away, separated from what's still to come."
    test_plan: "1. Open a project's Meetings view that has both scheduled and held/cancelled meetings. Confirm two sections: \"Upcoming (n)\" listing only scheduled meetings soonest-first, and \"Previous (n)\" listing held/cancelled most-recent-first. 2. Click a meeting in Previous → it opens to its details/minutes. 3. Mark an upcoming meeting held (confirm dialog, T-0335) → it moves from Upcoming to Previous on refresh. 4. A project with only upcoming meetings shows no Previous section; a brand-new project shows the \"No meetings yet\" empty state; a project whose only meetings are held shows \"No upcoming meetings\" plus the Previous section. 5. Check the same on the org-wide /meetings page and a pre-project's meetings — all split identically. Cancelled meetings appear under Previous with the struck-through badge."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T12:43:20Z
version: 8
collaborators:
  - kind: human
    name: Austin Pickering
---

Its difficult to find a meeting after it has been held we need a way to show them / add the the meetinngs view previous meetings

## Conversation

**2026-07-02 12:43 claude-code:** Run run-20260702-1243 completed — You can now easily find meetings that already happened. Before, every meeting sat in one long list sorted by date, so once a meeting was marked held it got mixed in and buried — people said it felt like held meetings "disappeared." The meetings view now has two clear sections: "Upcoming" (meetings still scheduled, next one first) and "Previous" (meetings that were held or cancelled, most recent first). The Previous section only appears when there are past meetings, and clicking any past meeting still opens it to read its details and minutes. This applies everywhere meetings are listed — inside a project, the org-wide meetings page, and pre-projects. If we'd left it, finding what was decided in a past meeting would stay a scrolling hunt. Benefit: past meetings are one glance away, separated from what's still to come.
