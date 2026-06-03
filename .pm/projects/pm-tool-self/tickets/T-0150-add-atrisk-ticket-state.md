---
id: T-0150
title: Add at_risk ticket state
type: feature
state: done
priority: p2
created: 2026-05-29T17:57:12Z
updated: 2026-06-02T15:50:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 21504
reporter: null
assignee: null
acceptance_criteria:
  - Ticket state enum + TicketState type include 'at_risk'
  - State machine allows in_progress/ready -> at_risk and at_risk -> in_progress/review/ready/done
  - Web surfaces at_risk with a warn tone + friendly 'At risk' label on board and list
  - A lint rule warns when a ticket sits in at_risk beyond N days
out_of_scope: []
code_anchors:
  - path: schemas/ticket.schema.json
  - path: cli/src/lib/state-machine.ts
  - path: web/app/_components/KanbanBoard.tsx
  - path: web/app/_lib/friendly-states.ts
  - path: linter/src/rules/at-risk.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: confirmed_for_release
source: charter
---

# T-0150: Add at_risk ticket state

## Problem

Big-rock #10 (missing). Add `at_risk` to the ticket state enum + state-machine transitions + warn-tone UI + a stuck->N-days lint.

## Acceptance criteria

_To define at sprint promotion._
