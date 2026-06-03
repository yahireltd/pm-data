---
id: T-0169
title: "Collision-safe claiming: show in-progress + who-owns-it where work is picked, warn on double-pickup"
type: feature
state: done
priority: p1
created: 2026-06-02T17:13:55Z
updated: 2026-06-02T17:43:26Z
project: pm-tool-self
section: null
parent: null
children: []
order: 40960
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - claimTicket action claims a ticket (assignee + in_progress); if already in_progress by someone else it returns a take-over warning instead of silently double-assigning
  - A Claim control shows in-progress-by-X and a Claim / Take over button, using the current author
  - Surfaced where work is picked - the Product Backlog rows and Sprint committed items show who is on each ticket + a claim affordance
  - web tsc clean; lint 0 errors
out_of_scope: []
code_anchors:
  - path: web/app/_actions/tickets.ts
  - path: web/app/_components/ClaimControl.tsx
  - path: web/app/_components/ProductBacklog.tsx
  - path: web/app/_components/SprintsList.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: confirmed_for_release
source: discovered
---
# T-0169: Collision-safe claiming

## Problem

Work-in-progress isn't surfaced where work is picked, so two people (or an agent + person) can grab the same task. Today the claim (assignee + state=in_progress) is the soft lock + 'who's on this' signal, but: (1) nobody is prompted to claim; (2) claiming is wired to the dead `ready` state, not the Sprint/Backlog flow; (3) nothing warns on a double-pickup; (4) /watch + the board 'Agent working' badge only light up for claimed work, so agent activity is invisible unless claimed. Build: surface 'in progress by X' on Backlog/Sprint/Board, a claim/take-over flow from those surfaces, a warn-on-already-claimed guard, and reconcile claiming with the sprint/backlog. Pairs with T-0162 (remote MCP needs real locking for true multi-user concurrency).

## Acceptance criteria

_To define at sprint promotion._
