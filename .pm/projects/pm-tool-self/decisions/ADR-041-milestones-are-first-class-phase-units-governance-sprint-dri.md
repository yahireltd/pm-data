---
id: ADR-041
slug: milestones-are-first-class-phase-units-governance-sprint-dri
title: "Milestones are first-class phase units: governance + sprint-driven delivery"
state: accepted
decided: 2026-06-16
decided_by:
  - Austin
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0393
  - T-0394
version: 1
---

## Context

A large initiative (the Sales transformation, P-0018) is delivered in phases, and we model each phase as a milestone. But milestones were thin — a title, a date, and acceptance criteria — with no owner, no stakeholders, no way to hold a review against them, and no link to the sprints and tickets that actually deliver them. A phase had nowhere to hang its accountability or its work.

## Decision

Treat a milestone as the first-class unit of a phase, carrying both its governance and its delivery.

- **Governance.** A milestone has an accountable owner and its own stakeholders, and meetings can be booked against it (phase / gate reviews).
- **Delivery.** A milestone's acceptance criteria are its definition of done. Work is delivered through sprints, and a phase is delivered by one or more sprints — a sprint is a short time-box (2 weeks), a phase is months. "Plan a sprint" from a milestone: pick which criteria to tackle this iteration, and the tool creates a sprint linked to the milestone, seeded with a ticket per chosen criterion (each carrying the criterion as its acceptance criterion and committed to the sprint). Tickets and sprints both carry a `milestone` link, so a milestone rolls up its sprints and its ticket coverage.

## Consequences

- The milestone is the spine: criteria (outcomes) → sprints (time-boxed commitments) → tickets (work), with rollup in both directions.
- Sprints stay short and honest — you commit a slice you can finish, not the whole phase. A criterion usually breaks into several tickets.
- New capabilities ship in web AND MCP: milestone owner + stakeholders (T-0393), milestone meetings (T-0394), and plan-a-sprint-from-milestone.
- "Phase" is reserved for the lifecycle; delivery chunks are "milestones / slices", delivered by sprints.
- Relies on the run-vs-change split (ADR-040): the Sales initiative ships into the Yasystem repo, and its phases are these milestones.