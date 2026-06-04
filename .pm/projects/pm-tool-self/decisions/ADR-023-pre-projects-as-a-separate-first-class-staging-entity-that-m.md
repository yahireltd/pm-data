---
id: ADR-023
slug: pre-projects-as-a-separate-first-class-staging-entity-that-m
title: Pre-Projects as a separate first-class staging entity that must pass intake/planning gates before converting to a project
state: accepted
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0198
---

## Context
Ideas arrive (often from a ticket or meeting) that are not yet projects but shouldn't be lost or prematurely promoted into the full project machinery. We could have (a) created a real project in `planning` state for every idea and let it sit — but that pollutes the project list with half-baked ideas and lets them skip real scoping; or (b) tracked ideas as ordinary tickets/labels, which gives them no kickoff/planning structure.

## Decision
Introduce PreProject as its own org-level entity (PP-NNN, schemas/pre-project.schema.json, lives at .pm/pre-projects/) with its own lifecycle: draft -> planning -> ready -> converted. It is creatable from a ticket (source_tickets), and must be forced through kickoff + planning before convert-to-project sets its `project` slug. It deliberately reuses the intake/planning gating concept rather than being a project in disguise.

## Consequences
A new entity, id namespace, storage location, and a future conversion path to build and keep consistent with Project — more surface than a project-state flag would have been. In return, the project list stays clean (only real projects), ideas get a real scoping gate before they earn project status, and the staging-vs-project boundary is structural. The convert-to-project gates are a later slice, so until then a converted PreProject and its project must be kept in sync manually.