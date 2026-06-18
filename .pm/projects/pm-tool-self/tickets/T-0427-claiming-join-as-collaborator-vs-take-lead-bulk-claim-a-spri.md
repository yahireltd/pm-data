---
id: T-0427
title: "Claiming: join as collaborator vs take lead, bulk-claim a sprint, and surface my collaborations"
type: feature
state: done
created: 2026-06-18T16:22:32Z
updated: 2026-06-18T18:10:57Z
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
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Claiming offers two clear modes: join as a collaborator (keeps the current lead, normally the agent) OR take it as the main dev (become the primary assignee)."
  - "[x] Claiming as a collaborator never removes the existing primary assignee (e.g. claude stays the lead)."
  - "[x] A whole sprint's committed tickets can be claimed (as collaborator) in one action."
  - "[x] The 'My work' views include tickets where I'm a collaborator, not just where I'm the primary assignee: /me and the /tickets 'mine' assignee filter both match collaborator membership."
  - "[x] It's visible whether I'm the lead or a collaborator on a ticket in these views."
out_of_scope: []
code_anchors:
  - path: web/app/_components/ClaimControl.tsx
    note: "two-mode claim: join as collaborator vs take lead"
  - path: web/app/_actions/tickets.ts
    symbol: claimAsCollaborator / claimTicketsAsCollaborator
    note: idempotent single + bulk collaborator claim
  - path: web/app/_components/SprintsList.tsx
    note: Claim all (bulk) button + pass collaborators
  - path: web/app/me/page.tsx
    note: My tickets includes collaborations + lead/collab tag
  - path: web/app/tickets/page.tsx
    note: assignee 'mine' filter matches collaborator too
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260618-1631
    model: claude-opus-4-8
    started: 2026-06-18T16:31:55Z
    status: completed
    ended: 2026-06-18T16:32:13Z
    summary: "Made it easy to get involved in a ticket without taking it away from whoever's leading it, and to surface your own work. The agent is normally the main developer on a ticket; now when you want in, the main \"Claim\" action adds you as a collaborator (\"also on this\") and leaves the agent as the lead — there's a separate, quieter \"as lead\" if you actually want to take it over as the main dev. You can also claim a whole sprint in one click: a \"Claim all\" button on each sprint joins you (as a collaborator) to every ticket committed in it, skipping finished ones and any you're already on. And the \"My work\" views now count collaborations, not just tickets you're the primary owner of: your \"My work\" page and the \"mine\" filter on the All-tickets page both show tickets where you're a collaborator, each tagged so you can tell at a glance whether you're the lead or a collaborator. Before this, claiming replaced the existing owner (so you couldn't quietly join), there was no way to claim a sprint in bulk, and tickets you were only collaborating on were invisible in your personal views. Benefit: people can join the agent's work as a second pair of eyes without disrupting it, sign up for a sprint in one click, and actually see everything they're involved in."
    test_plan: |-
      Claim modes (a ticket on the Backlog or Sprint view, where ClaimControl shows):
      1. On a ticket led by the agent (or anyone else), you see the lead's name plus two actions: "Claim" (join as collaborator) and "as lead" (take over).
      2. Click "Claim" — you're added as a collaborator, the lead is UNCHANGED (e.g. claude stays). The control now shows "you're also on this" with a "leave" option.
      3. Click "as lead" on someone else's ticket — it asks to confirm a take-over, then makes you the primary. On an unassigned ticket, "Claim" makes you the lead directly (with an "as collab" alternative).
      4. Tapping "Claim" twice doesn't error (idempotent).

      Bulk sprint claim (Projects → a project → Sprints):
      5. Each sprint with committed tickets has a "Claim all" button. Click it — you're joined as a collaborator on all its tickets; a small message reports how many were joined / already on. Finished tickets are skipped.
      6. Requires your name set (top-right); if unset it tells you so.

      My work surfacing:
      7. /me "My tickets" lists tickets where you're the lead OR a collaborator, each tagged "lead" or "collab".
      8. /tickets → Assignee → "mine" includes collaborator tickets, not just ones you're primary on.

      Cross-impact: ClaimControl is shared by the Backlog and Sprint surfaces; confirm both still render and claim works. The collaborators model (assignee + collaborators) is unchanged underneath.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ui
  - workflow
  - dogfood
  - for-austin
attention: null
version: 12
branch: facelift/rbac-look
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

## Conversation

**2026-06-18 16:32 claude-code:** Run run-20260618-1631 completed — Made it easy to get involved in a ticket without taking it away from whoever's leading it, and to surface your own work. The agent is normally the main developer on a ticket; now when you want in, the main "Claim" action adds you as a collaborator ("also on this") and leaves the agent as the lead — there's a separate, quieter "as lead" if you actually want to take it over as the main dev. You can also claim a whole sprint in one click: a "Claim all" button on each sprint joins you (as a collaborator) to every ticket committed in it, skipping finished ones and any you're already on. And the "My work" views now count collaborations, not just tickets you're the primary owner of: your "My work" page and the "mine" filter on the All-tickets page both show tickets where you're a collaborator, each tagged so you can tell at a glance whether you're the lead or a collaborator. Before this, claiming replaced the existing owner (so you couldn't quietly join), there was no way to claim a sprint in bulk, and tickets you were only collaborating on were invisible in your personal views. Benefit: people can join the agent's work as a second pair of eyes without disrupting it, sign up for a sprint in one click, and actually see everything they're involved in.

---

**2026-06-18 18:10 — you**

working
