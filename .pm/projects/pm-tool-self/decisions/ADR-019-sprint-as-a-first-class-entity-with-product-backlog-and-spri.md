---
id: ADR-019
slug: sprint-as-a-first-class-entity-with-product-backlog-and-spri
title: Sprint as a first-class entity, with product-backlog and sprint-backlog as separate planning horizons
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0126
  - T-0152
  - T-0163
  - T-0204
---

## Context
The tool needed to model iterative delivery (course modules on Sprint Definition/Planning). The question was where committed work lives. We could have (a) reused ticket labels/a `sprint` field on each ticket as the only linkage, leaving 'what's in this sprint' as a derived query; or (b) treated 'the backlog' as one flat ranked list and let a sprint be just a date range. Both blur the line the course insists on: the product backlog (everything ranked, still negotiable) versus the sprint backlog (locked commitment for this iteration).

## Decision
Introduce a standalone Sprint entity (SPR-NNN, schemas/sprint.schema.json) that OWNS its committed work via `committed_items` — the sprint backlog is the array on the sprint, not a flag on the ticket. The product-backlog horizon is a separate, orthogonal `backlog_status` field on the ticket (in_review / confirmed_for_release / parked / rejected) that has nothing to do with the workflow `state` machine. Commit requires the ticket to carry acceptance criteria (no wishes in a sprint); start requires non-empty committed_items; complete requires a recorded `velocity_actual` so committed-vs-actual stays the calibration point. New work mid-sprint goes to the product backlog, never absorbed into the locked sprint.

## Consequences
Two backlogs and a third state axis (workflow state, defect_status, backlog_status) is more conceptual surface and more places a ticket's 'status' can be read from — contributors must learn that a ticket in a sprint is tracked by the sprint's committed_items, not by backlog_status. In exchange, scope-lock is structural rather than a convention, velocity is forced to be measured, and 'what did we commit to' is a fact on disk. Quick-create-into-sprint from the backlog (T-0204) became a natural follow-on once the entity existed.