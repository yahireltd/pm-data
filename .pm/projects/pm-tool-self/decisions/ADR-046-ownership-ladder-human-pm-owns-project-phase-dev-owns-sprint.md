---
id: ADR-046
slug: ownership-ladder-human-pm-owns-project-phase-dev-owns-sprint
title: "Ownership ladder: human PM owns project/phase, dev owns sprint, agent owns ticket"
state: accepted
decided: 2026-06-23
decided_by:
  - Austin
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0458
version: 1
---

## Context

As agents do the coding, the developer's role shifts toward project management — and the project's stated direction is that the new bottleneck is human judgment (decisions / feedback / testing / iteration). The team needed accountability expressed at the right altitude. Sprints had no owner; only tickets carried an assignee, so there was no level that a dev clearly "owned and managed".

## Decision

Ownership exists at **every level** of the hierarchy, each meaning something different:

- **Project / Phase owner = the human PM** — accountable for the stage's outcome and for clearing its decisions, feedback and test loops.
- **Sprint owner = the DEV** — the unit of work a developer owns end-to-end and manages; agents do the tickets inside it.
- **Ticket assignee = the AGENT** — the executor.

Add `sprint.owner` (mirrors milestone/phase ownership). The per-dev **Roadmap** aggregates each dev's owned sprints across all projects, laid out over time.

## Consequences

- **+** Maps the "devs become PMs, agents code" model directly onto the data; the sprint is the natural unit a dev owns and reports on.
- **+** Makes the per-dev roadmap possible (sprint owner + sprint start/end dates).
- **+** A clear answer to "who owns this?" at every rung.
- **−** Another owner field; ownership is set manually, not derived. The roadmap is only as good as the owners + dates entered.