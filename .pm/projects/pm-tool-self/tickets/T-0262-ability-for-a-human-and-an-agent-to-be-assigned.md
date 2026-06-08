---
id: T-0262
title: ability for a human and an agent to be assigned
type: feature
state: review
priority: p2
created: 2026-06-05T22:20:31Z
updated: 2026-06-08T15:30:31Z
project: pm-tool-self
section: null
parent: null
children: []
order: 84992
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - allow human and agent to both be assigned - give us stronger icons and fonts anywhere we display user and stakeholders
out_of_scope: []
code_anchors:
  - path: schemas/ticket.schema.json
  - path: cli/src/types.ts
    symbol: Ticket
  - path: web/app/_components/Avatar.tsx
  - path: web/app/_components/CollaboratorsEditor.tsx
  - path: web/app/_actions/tickets.ts
    symbol: addCollaborator
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1451
    model: claude-opus-4-8
    started: 2026-06-08T14:51:22Z
    status: completed
    ended: 2026-06-08T14:51:48Z
    summary: "A ticket can now show more than one person working on it — for example you and your AI agent, or you and a colleague and their agent — instead of just a single name. We kept the existing \"main owner\" (the one the tool uses to track who claimed the work and ran it) and added an optional \"Also on this\" list beside it, where you can add other people or agents and remove them. Anyone you add is for visibility only; it doesn't change how the work is claimed or closed, so nothing about the existing workflow breaks. We also made the little person icons throughout the app much clearer: agents now carry a small robot mark, customers a help mark, with colour rings and bolder names, so at a glance you can tell a human apart from an agent wherever names appear. Why it matters: when two people (each with their own agent) are working around the same ticket, the board now reflects that reality instead of hiding everyone but one name. The approach was written up as a decision record (ADR-034) so the \"one main owner + collaborators\" choice is on the record."
    test_plan: |-
      1. Open any ticket's detail page → a new "Also on this" row sits under "Assignee".
      2. Click "Add", type a name, choose Person or Agent, Add → they appear as a chip with a clear icon (agents show a robot badge). Add a second of the other kind to confirm a human and an agent can both be on it.
      3. Remove one via the ✕ on its chip.
      4. Try adding the current assignee, or the same name twice → refused with a clear message.
      5. On a board/list, a ticket with collaborators shows a small overlapping stack of avatars (primary + others, then +N); without collaborators it shows the single assignee as before.
      6. Across the app (rows, cards, stakeholder lists), agent avatars now show a robot mark + indigo ring, customers a help mark + amber ring, names are bolder.
      7. The main owner still drives claiming/running/closing exactly as before — collaborators don't affect state.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Documented the new collaborators field in SCHEMA.md; ADR-034 records the design.
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-08T14:51:48Z
collaborators:
  - kind: human
    name: austin
---

from any of the admin or project team for asignee. Zsolt may be working on somehing with his agent at the same time as me with my agent

## Conversation

**2026-06-08 14:51 claude:** Run run-20260608-1451 completed — A ticket can now show more than one person working on it — for example you and your AI agent, or you and a colleague and their agent — instead of just a single name. We kept the existing "main owner" (the one the tool uses to track who claimed the work and ran it) and added an optional "Also on this" list beside it, where you can add other people or agents and remove them. Anyone you add is for visibility only; it doesn't change how the work is claimed or closed, so nothing about the existing workflow breaks. We also made the little person icons throughout the app much clearer: agents now carry a small robot mark, customers a help mark, with colour rings and bolder names, so at a glance you can tell a human apart from an agent wherever names appear. Why it matters: when two people (each with their own agent) are working around the same ticket, the board now reflects that reality instead of hiding everyone but one name. The approach was written up as a decision record (ADR-034) so the "one main owner + collaborators" choice is on the record.
