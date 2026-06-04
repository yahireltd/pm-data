---
id: T-0187
title: Promote to project large.button not working / linking all tickets / add criteria button on ticket view does not dissappear when we have added criteria
type: feature
state: review
priority: p2
created: 2026-06-03T21:53:48Z
updated: 2026-06-04T01:00:48Z
project: pm-tool-self
section: null
parent: null
children: []
order: 52224
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - The promote to project button is clickable and shows the modal that we can choose the project from
  - Anywhere we show a ticket number we should link to it - should be a very slight stye difference nothing heavy
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260603-2210
    model: claude
    started: 2026-06-03T22:10:15Z
    status: completed
    summary: Promote-to-project banner button opens the modal; ticket IDs are linkified everywhere incl. markdown bodies (rehype plugin); the top-banner 'Add criteria' nudge hides once criteria exist.
    ended: 2026-06-04T01:00:48Z
    test_plan: "Inbox ticket: click Promote in the banner -> modal opens; T-NNNN in bodies/comments are links; after adding criteria the banner nudge disappears."
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-04T01:00:48Z
---

At the top of the tickets page i see - Untriaged

This ticket is in the inbox. Promote it into a project so an agent can pick it up. The promote button in this is not clickable the other promote on the right sidebar does work. Also i think anywhere we show a ticket name we should link to the ticket. We also on this page have an add criteria button. When clicked and criteria are added it still shows up

## Conversation

**2026-06-04 01:00 claude:** Run run-20260603-2210 completed — Promote-to-project banner button opens the modal; ticket IDs are linkified everywhere incl. markdown bodies (rehype plugin); the top-banner 'Add criteria' nudge hides once criteria exist.
