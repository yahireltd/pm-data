---
id: T-0373
title: A way to see meetings that were already held
type: feature
state: review
priority: p2
created: 2026-06-15T14:51:32Z
updated: 2026-07-02T19:02:34Z
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
  - "[x] Held (past) meetings remain visible in the meetings view rather than disappearing once held"
  - "[x] Past meetings are separated from upcoming ones (e.g. a 'previous meetings' section or filter)"
  - "[x] A past meeting can be opened from that view to see its details/minutes"
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
    ended: 2026-07-02T19:02:25Z
    summary: "Past meetings are now findable from the main Meetings page too, not just inside each project. First round split every meetings list into \"Upcoming\" and \"Previous\" so held meetings stopped disappearing — but the Meetings page in the left navigation only ever listed org-wide meetings, so a project's past meetings were invisible from the main nav (Austin's send-back). Now that page shows every meeting you're allowed to see — org-wide and from every project — still split into Upcoming and Previous, with each row labelled with its home project (or \"Org-wide\") and opening in the right place. Creating a meeting from that page still makes an org-wide one; a project's meetings are created on its own page. If we'd left it, \"where are the minutes from that meeting?\" would still mean remembering which project it lived in first. Benefit: one place to find any meeting, past or upcoming."
    test_plan: "1. After deploy, open Meetings from the left nav. It should now list meetings FROM YOUR PROJECTS as well as org-wide ones, in two sections: Upcoming (soonest first) and Previous (held/cancelled, most recent first). 2. Each row shows a small chip naming its home project (or \"Org-wide\"); clicking a project meeting opens it under /projects/<slug>/meetings/…, an org one under /meetings/…. 3. Find the meeting you marked held earlier — it should appear under Previous with its minutes reachable. 4. \"New meeting\" on this page still creates an org-wide meeting. 5. Work mode / access: projects hidden by work mode (or that a member can't see) contribute no meetings here. 6. Regression: a project's own Meetings tab and pre-project meetings are unchanged (split still works there); meeting detail pages unaffected."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T19:02:25Z
version: 15
collaborators:
  - kind: human
    name: Austin Pickering
---

Its difficult to find a meeting after it has been held we need a way to show them / add the the meetinngs view previous meetings

## Conversation

**2026-07-02 12:43 claude-code:** Run run-20260702-1243 completed — You can now easily find meetings that already happened. Before, every meeting sat in one long list sorted by date, so once a meeting was marked held it got mixed in and buried — people said it felt like held meetings "disappeared." The meetings view now has two clear sections: "Upcoming" (meetings still scheduled, next one first) and "Previous" (meetings that were held or cancelled, most recent first). The Previous section only appears when there are past meetings, and clicking any past meeting still opens it to read its details and minutes. This applies everywhere meetings are listed — inside a project, the org-wide meetings page, and pre-projects. If we'd left it, finding what was decided in a past meeting would stay a scrolling hunt. Benefit: past meetings are one glance away, separated from what's still to come.

**2026-07-02 19:02 claude-code:** Send-back addressed (commit 3ed8c90). The problem wasn't the Upcoming/Previous split — it was that the main Meetings page (left nav) only ever listed org-wide meetings, so a project's past meetings could never appear there. That page is now a global index: every meeting you can see, org-wide and from all your projects, still split Upcoming/Previous, each row labelled with its home project and opening in the right place. "New meeting" there still creates an org-wide meeting; project meetings are created on their project page. Re-test after deploy: open Meetings from the left nav and look for the meeting you marked held — it should be under Previous with a project chip, one click from its minutes.
