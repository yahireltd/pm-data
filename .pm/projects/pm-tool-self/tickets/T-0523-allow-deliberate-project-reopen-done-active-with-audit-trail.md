---
id: T-0523
title: Allow deliberate project reopen (done → active) with audit trail — resurrection is currently hard-blocked
type: feature
state: triaged
created: 2026-07-08T12:03:52Z
updated: 2026-07-08T12:03:52Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: hit the gate live while resuming route-planner work
assignee: null
acceptance_criteria:
  - A done project can be reopened via MCP + web UI with a mandatory reason
  - Cancelled projects still cannot be resurrected
  - Reopen is recorded in an audit trail visible on the project
  - P-0007 successfully reopened and later closed through the normal gates
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem
`pm_update_project state=active` on a `done` project fails: "illegal project transition done -> active (projects in done/cancelled cannot be resurrected)". Correct as a default — but P-0007 (Logistics Route Planning rollout) is a live counter-example: it was closed ADMINISTRATIVELY on 2026-06 (folded into yasystem during the initiatives-vs-systems reorganisation), not closed through the gates. Remaining work has now resumed (meetings still to run, open tickets T-0221/0371/0383/0401/0505/0506, and Austin wants to eventually close it properly THROUGH the gates as a dogfood test). The tool cannot express "we closed this too early".

## Proposal
A deliberate `reopen` operation rather than loosening the transition matrix:
- Only `done → active` (never from `cancelled`).
- Requires a reason string; writes an audit entry / status note ("Reopened 2026-07-08 by austin: closed administratively before gates were run").
- Surfaces in the project header (reopened badge + reason) so a reopened project is visibly different from a never-closed one.
- Closure gates run fresh on the next close.

## Why now
Blocking the route-planner workstream from resuming in its own project; the workaround (a phase-2 project or working in yasystem) fragments the ticket history a second time (its 7 original tickets already moved once).

## Acceptance criteria
- A done project can be reopened via MCP + web UI with a mandatory reason
- Cancelled projects still cannot be resurrected
- Reopen is recorded in an audit trail visible on the project
- P-0007 successfully reopened and later closed through the normal gates