---
id: T-0198
title: Pre-Projects
type: feature
state: review
priority: p2
created: 2026-06-04T00:08:35Z
updated: 2026-06-05T15:40:50Z
project: pm-tool-self
section: null
parent: null
children: []
order: 55296
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - pre-project view and menu link needed
  - pre-projects need to be forced through the kickoff and planning stages
  - we need to be able to make a pre-project a project and carry over the data
  - we want to be able to recieve a ticket - make it a pre-project, then do the kick off and scoptin and make it a real project then continue the workflow
  - we need to be able to plan the required meetings
  - the stakeholders need to use the same roster and have emails that we can use to invite to meetings as we do in the standalone meetings and project meeting
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0010
    model: claude
    started: 2026-06-04T00:10:50Z
    status: completed
    summary: Pre-projects can now do everything a real project can for planning people and meetings. Their stakeholders are now full contacts with an email (drawn from the shared address book), so they can actually be invited to things. And you can now plan and hold meetings on a pre-project — the kickoff and scoping sessions that planning needs. When a pre-project becomes a real project, its meetings move across automatically. This closes the gap where you couldn't properly plan a project until after it already existed.
    ended: 2026-06-05T15:40:50Z
    test_plan: 1. Open a pre-project, add a stakeholder with an email — confirm it saves with the email and shows up in the shared people list elsewhere. 2. Plan a meeting on the pre-project with that person as an attendee — confirm it's created and the invite email sends. 3. Confirm the pre-project's meetings do NOT clutter the general org-wide meetings list. 4. Convert the pre-project to a real project — confirm the meeting now appears under that project and no longer references the pre-project.
    records:
      docs: updated
      tech_session: TS-007
      status_note: none-needed
      docs_note: SCHEMA.md Meeting section documents the new pre_project field; types + schema updated together.
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T15:40:50Z
---

we need a way to create a pre-project- this must have all the planning filled in before becomming a project. We should be able to make a pre-project out of tickets or add to pre-project

## Conversation

**2026-06-05 15:40 claude:** Run run-20260604-0010 completed — Pre-projects can now do everything a real project can for planning people and meetings. Their stakeholders are now full contacts with an email (drawn from the shared address book), so they can actually be invited to things. And you can now plan and hold meetings on a pre-project — the kickoff and scoping sessions that planning needs. When a pre-project becomes a real project, its meetings move across automatically. This closes the gap where you couldn't properly plan a project until after it already existed.
