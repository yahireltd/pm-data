---
id: ADR-043
slug: parking-lot-a-per-project-parking-lot-section-not-a-new-stat
title: Parking lot = a per-project "Parking Lot" section, not a new state
state: accepted
decided: 2026-06-18
decided_by:
  - Austin
  - Ben
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0422
version: 1
---

## Context

`triaged` is one undifferentiated bucket — genuine next-up work, half-formed ideas, and "not now" items all pile together, so you can't tell what's actually next from someday/maybe. We want a first-class place to park tickets that isn't `wontfix` ("we decided no") and isn't a pre-project (a whole future project being shaped).

Options weighed (T-0422): (A) a new `parked` lifecycle state; (B) a reserved `parked` label; (C) a dedicated per-project section; (D) an orthogonal `parked` flag + global view.

## Decision

Use **(C): a dedicated, reserved "Parking Lot" section per project.** A ticket is "parked" when it lives in that section. It reuses the existing sections mechanism (tickets already carry a `section`, and the List view already drags tickets between sections), so the change is contained and there's no state-machine surgery. The section is flagged (`parking_lot: true`), rendered distinctly and collapsed by default, kept out of the active board groups, and its tickets are separated out of the default backlog lists so they don't clutter "what's next."

## Consequences

- Parking is per-project and manual (you move a ticket into the section) — accepted as the cost of the lightest, lowest-risk mechanism.
- A parked ticket keeps its real state and full history; un-parking is just moving it back out.
- Visibly distinct from `wontfix` (a closed state) and from next-up work (its own collapsed, labelled section).
- **Boundary with pre-projects:** a pre-project is a *future project* being shaped through intake gates; the parking lot is a *ticket-level* "not now." They stay separate concepts.
- If per-project proves too manual later, this can be revisited toward a global flag/view (option D) without throwing away the section data.