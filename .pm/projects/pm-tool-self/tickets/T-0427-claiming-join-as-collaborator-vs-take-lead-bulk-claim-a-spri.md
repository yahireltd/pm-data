---
id: T-0427
title: "Claiming: join as collaborator vs take lead, bulk-claim a sprint, and surface my collaborations"
type: feature
state: triaged
created: 2026-06-18T16:22:32Z
updated: 2026-06-18T16:22:32Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: web
  contact: austin@yahire.com
assignee: null
acceptance_criteria:
  - "Claiming offers two clear modes: join as a collaborator (keeps the current lead, normally the agent) OR take it as the main dev (become the primary assignee)."
  - Claiming as a collaborator never removes the existing primary assignee (e.g. claude stays the lead).
  - A whole sprint's committed tickets can be claimed (as collaborator) in one action.
  - "The 'My work' views include tickets where I'm a collaborator, not just where I'm the primary assignee: /me and the /tickets 'mine' assignee filter both match collaborator membership."
  - It's visible whether I'm the lead or a collaborator on a ticket in these views.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ui
  - workflow
  - dogfood
  - for-austin
attention: null
version: 1
---

## Problem

Follow-on to T-0420. Two friction points found dogfooding the claim flow:

- **Claiming takes over the ticket.** The agent (claude) is normally the main assignee/dev. When a human wants to get involved, claiming **replaces** the primary assignee — there's no easy "I'm also on this" that keeps the agent as the lead. We want to claim at the **"also assigned to" (collaborator)** level by default, and a separate explicit **"take as main dev"** when we really want the lead.
- **No bulk claim.** Claiming every ticket in a sprint one-by-one is tedious. We want to **claim a whole sprint** (as collaborator) in one action.
- **"My work" misses collaborations.** The personal views only match the *primary* assignee, so tickets where I'm a **collaborator** don't show up. The `/me` list and the `/tickets` "mine" filter should include collaborator membership, and show whether I'm lead or collaborator.

## Proposed

- ClaimControl: a prominent **Claim** (join as collaborator, lead unchanged) + a quieter **as lead** (become primary, with the existing take-over guard).
- A **Claim all** action on each sprint card → adds me as a collaborator to its committed tickets (skips closed / ones I'm already on).
- Broaden the "mine" match (in `/me` and the `/tickets` assignee filter) to **assignee OR collaborator**, with a small lead/collab indicator.

## Note

The model already has `assignee` (primary) + `collaborators` ("also on this", ADR-034 / T-0262) and an `addCollaborator` action — this is mostly wiring those into the claim UX and the personal views.