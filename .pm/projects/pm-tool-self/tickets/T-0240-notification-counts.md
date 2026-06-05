---
id: T-0240
title: Notification counts
type: feature
state: done
priority: p2
created: 2026-06-05T17:12:08Z
updated: 2026-06-05T17:46:02Z
project: pm-tool-self
section: null
parent: null
children: []
order: 72704
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Notification counts should be red background"
  - "[x] Ticket count should only be un-triaged count"
out_of_scope: []
code_anchors:
  - path: web/app/layout.tsx
  - path: web/app/_components/TopNavLinks.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1736
    model: claude
    started: 2026-06-05T17:36:27Z
    status: completed
    summary: The little count badges in the top menu are now red, so they read as notifications you should action (Inbox, Review, Ready, In progress) rather than quiet grey chips. And the Tickets badge now shows only how many tickets still need triaging, instead of the grand total — so it's a useful "X to triage" nudge instead of a static number that never really changes.
    ended: 2026-06-05T17:44:54Z
    test_plan: "Look at the top bar: the counts on Tickets/Inbox/Review/Ready/In progress show as red badges (and only when the number is above zero). The Tickets number should equal the count of un-triaged tickets (those still in the inbox state), not the total number of tickets. (The sidebar \"Projects\" total stays grey on purpose — it's informational, not a notification.)"
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

Notification counts should be red background \
Ticket count should only be un-triaged count

## Conversation

**2026-06-05 17:44 claude:** Run run-20260605-1736 completed — The little count badges in the top menu are now red, so they read as notifications you should action (Inbox, Review, Ready, In progress) rather than quiet grey chips. And the Tickets badge now shows only how many tickets still need triaging, instead of the grand total — so it's a useful "X to triage" nudge instead of a static number that never really changes.

---

**2026-06-05 17:46 — you**

Working
