---
id: T-0357
title: pre-project meeting minutes are being sent to all stakeholders
type: feature
state: review
priority: p2
created: 2026-06-11T21:29:39Z
updated: 2026-07-02T12:29:50Z
project: pm-tool-self
section: null
parent: null
children: []
order: 105472
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - They should not be sent to anone who has not susbscribed to updates
out_of_scope: []
code_anchors:
  - path: comms/src/resolve.ts
    symbol: notifyOnMatches (empty-notify_on default)
  - path: comms/tests/dispatch.test.ts
    symbol: minutes are opt-in (T-0357) test
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1229
    started: 2026-07-02T12:29:37Z
    status: completed
    ended: 2026-07-02T12:29:50Z
    summary: "Meeting minutes now go only to people who chose to receive them, not to everyone on the meeting. Before, if someone was listed on a meeting but hadn't picked which updates they wanted, the system treated silence as \"send them everything\" — so when a pre-project meeting was marked held, its minutes emailed the entire stakeholder register, including people who never asked for them. Now the minutes (the \"held\" and \"outcome recorded\" emails) are opt-in: only stakeholders who tick \"meeting held\" or \"outcome recorded\" on their entry get them. Meeting reminders and calendar invites are unaffected — those still reach attendees, because there's no way to opt into a reminder by hand. If we'd left it, every future pre-project meeting would keep spamming its whole register with minutes, which is exactly the complaint. Benefit: minutes reach the right, consenting people and no one else."
    test_plan: "1. Open a meeting with several stakeholders where at least one has NO \"Notify on\" selections and at least one has ticked \"meeting held\" / \"outcome recorded\". 2. Mark the meeting \"held\" (and/or record an outcome). 3. Confirm via the comms log (.pm/.comms-log/) or inboxes that ONLY the stakeholders who subscribed to meeting_held/outcome_recorded were emailed the minutes — the ones with empty Notify-on were NOT. 4. Confirm meeting reminders still reach attendees regardless of Notify-on (T-0197 unaffected) and calendar invites/meeting_scheduled behaviour is unchanged. 5. Regression: ticket state-change and comment notifications still respect each stakeholder's notify_on as before. Automated: `bun test comms/` (see the new \"minutes are opt-in (T-0357)\" case in dispatch.test.ts)."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-02T12:29:50Z
version: 8
collaborators:
  - kind: human
    name: Austin Pickering
---

The behaviour should be that it only goes to those stakeholder that have subscribed to that action

## Conversation

**2026-07-02 12:29 claude-code:** Run run-20260702-1229 completed — Meeting minutes now go only to people who chose to receive them, not to everyone on the meeting. Before, if someone was listed on a meeting but hadn't picked which updates they wanted, the system treated silence as "send them everything" — so when a pre-project meeting was marked held, its minutes emailed the entire stakeholder register, including people who never asked for them. Now the minutes (the "held" and "outcome recorded" emails) are opt-in: only stakeholders who tick "meeting held" or "outcome recorded" on their entry get them. Meeting reminders and calendar invites are unaffected — those still reach attendees, because there's no way to opt into a reminder by hand. If we'd left it, every future pre-project meeting would keep spamming its whole register with minutes, which is exactly the complaint. Benefit: minutes reach the right, consenting people and no one else.
