---
id: TS-017
slug: milestone-governance-sprint-planning-sales-project-setup
title: Milestone governance, sprint planning & Sales project setup
project: pm-tool-self
created: 2026-06-16T19:42:26Z
updated: 2026-06-16T19:44:34Z
decisions:
  - "ADR-040 — Run vs change: the Sales work is two things, not one. Yasystem stays the live system we run and maintain; the transformation is a separate initiative (P-0018) that ships into Yasystem's repo. Ben's four phases are modelled as the initiative's milestones."
  - 'ADR-041 — Milestones are first-class phase units: each phase-milestone carries an owner, stakeholders and gate-review meetings, and is delivered through sprints that each pick which of its criteria to tackle. "Plan a sprint" from a milestone seeds a ticket per chosen criterion and commits them; tickets and sprints link back to the milestone for rollup.'
actions:
  - text: Ratify ADR-040 (run vs change) at the M-009 design review tomorrow.
  - text: Demo Phase 1 sprint planning tomorrow (Plan a sprint from MS-001), then build the M-009 gap features starting with the initiative→system link.
    ticket: T-0388
side_quests: []
problem: Our first real large initiative — the Sales transformation — showed the tool couldn't yet express phased delivery well. Milestones had no owner or stakeholders; you couldn't book a review meeting against a phase; you couldn't turn a phase into sprints; a converted project's overview and charter couldn't be edited outside the web; the milestone owner picker silently saved to the wrong project; and rewriting a milestone's criteria meant ~50 one-at-a-time edits. We worked through all of it in one sitting, using the Sales initiative as the live test case.
success_criterion: "The Sales initiative can be planned end to end in the tool: phases as milestones (each with an owner, stakeholders and gate-review meetings), each phase delivered through sprints that pick which criteria to tackle, and a plain-language overview/charter a non-technical reader understands — all drivable from the web AND by agents over MCP."
version: 3
---

# TS-017: Milestone governance, sprint planning & Sales project setup

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
