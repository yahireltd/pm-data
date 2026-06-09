---
id: SPR-029
slug: concurrency-safety-no-lost-edits-or-id-clashes
title: Concurrency safety — no lost edits or id clashes
project: pm-tool-self
state: in_progress
order: 29696
created: 2026-06-09T16:49:34Z
updated: 2026-06-09T16:52:40Z
committed_items:
  - T-0315
  - T-0316
  - T-0317
goal: "Make concurrent edits safe across web / CLI / MCP: race-safe id allocation, plus optimistic version checks so two people (or an agent) can't silently overwrite each other's changes."
start_date: 2026-06-09
end_date: 2026-06-20
velocity_history:
  - sprint: SPR-028
    actual: 3
  - sprint: SPR-025
    actual: 31
  - sprint: SPR-024
    actual: 4
capacity:
  total: 4
  unit: tickets
velocity_committed: 4
---

# SPR-029: Concurrency safety — no lost edits or id clashes

## Goal

_Make concurrent edits safe across web / CLI / MCP: race-safe id allocation, plus optimistic version checks so two people (or an agent) can't silently overwrite each other's changes._

## Planning notes

## Demo

## Retrospective
