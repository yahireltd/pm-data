---
id: T-0335
title: Marking a meeting "held" is one click and irreversible — add confirm + a way back
type: bug
state: triaged
created: 2026-06-10T00:12:38Z
updated: 2026-06-10T00:12:38Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - Flipping a meeting to held asks for confirmation (it is a one-way transition)
  - An admin can revert held back to scheduled when it was a mis-click (audited, like other admin overrides)
  - "Stretch: a pm_update_meeting_state MCP tool so agents can fix/operate meeting states too (known gap)"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ux
  - dogfood-finding
attention: null
version: 1
---

## Problem

The meeting state machine is deliberately one-way (scheduled → held → cancelled), but the web exposes "mark held" as a single un-confirmed click. Tonight Austin marked the WRONG meeting held (M-005, a future workshop) while intending M-006, and there was no way back in the UI — the legal-transitions table has no held → scheduled. It took a developer with SSH editing the file directly to undo one mis-click.

## Context

Real incident, 2026-06-10 ~00:10: two similar meetings under PP-002 (the Apr kickoff to be marked held + Thursday's upcoming workshop); the wrong one got the click. With more humans (sales team, Axel) about to use meetings heavily for the Sales Coherence programme, this will recur.

## Design notes

Confirm dialog on held (cheap); an admin-only "revert to scheduled" escape hatch with an audit note (the pattern the danger-zone actions already use). Related known gap: no MCP tool changes meeting state, so agents can't help fix these either.