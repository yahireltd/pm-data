---
id: T-0576
title: Moving a ticket's phase/milestone doesn't reconcile stale sprint membership (left in old phase's sprint)
type: bug
state: triaged
created: 2026-07-15T10:33:06Z
updated: 2026-07-15T10:33:06Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: dogfood
assignee: null
acceptance_criteria:
  - When a ticket's milestone/phase is changed to one in a DIFFERENT phase, the tool either auto-removes it from the now-mismatched sprint or clearly warns/prompts the user — it does not silently leave it committed to the old phase's sprint.
  - The normal 'move a ticket across phases' flow no longer dead-ends on the 'already in SPR-xxx — remove it first' error; the reconciliation is handled or offered at the point of the phase/milestone change.
  - A ticket committed to a sprint whose phase/milestone no longer matches the ticket is surfaced somewhere (warning/flag) rather than being an invisible inconsistency.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ticket-ui
  - sprints
  - milestones
  - dogfood
attention: null
version: 1
---

## Problem
Changing a ticket's **Phase / Milestone** via the ticket's "Where it sits" dropdowns does **not** reconcile its **sprint** membership. The ticket stays committed to the *old* phase's sprint, and you only discover the mismatch later via an *"already in SPR-xxx — remove it first"* error when trying to commit it to the new phase's sprint.

## Repro (what actually happened)
1. **T-0499** (project P-0018) was on **MS-001 (Phase 1)** and committed to **SPR-007** (a Phase-1 sprint).
2. Changed its **Phase → Phase 2** and **Milestone → Research Needed (MS-012)** via the ticket dropdowns. The change saved.
3. Tried to add it to a Phase-2 sprint (**SPR-010**) → error: **"T-0499 is already in SPR-007 — remove it there first."**
4. Net result: a Phase-2 ticket was silently still committed to a **Phase-1 sprint**, with nothing surfacing the inconsistency until the commit attempt failed.

## Expected
Sprints are milestone/phase-scoped, so moving a ticket to a different phase should reconcile the sprint — either **auto-uncommit** it from the now-mismatched sprint, or **prompt**: *"This ticket is still in SPR-007 (Phase 1) — remove it?"*. It should not leave a cross-phase inconsistency that only shows up as a downstream error.

## Notes
- Milestone/phase and sprint are currently treated as independent fields on the ticket; the fix is to reconcile them (or warn) when the phase/milestone moves.
- Code anchors: the ticket phase/milestone update handler + the sprint commit/membership logic (pm-tool repo) — exact paths TBD on pickup.