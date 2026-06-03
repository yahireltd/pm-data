---
id: T-0142
title: Defect lifecycle states + commands + lint rule
type: feature
state: done
priority: p2
created: 2026-05-29T13:28:10Z
updated: 2026-05-29T15:22:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 14336
reporter: null
assignee: null
acceptance_criteria:
  - Defects move through an explicit lifecycle (reported → triaged/confirmed → resolved/rejected) enforced by a state machine that rejects illegal transitions
  - A CLI command drives defect status changes and a lint rule flags defects in invalid or stalled states
out_of_scope: []
code_anchors:
  - path: cli/src/lib/state-machine.ts
    symbol: DEFECT_TRANSITIONS
    lines: 52-82
  - path: cli/src/commands/defect.ts
  - path: linter/src/rules/defect.ts
    symbol: checkDefect
  - path: web/app/_components/DefectStatusControl.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## Problem

_What's wrong / what's needed?_

## Context

_Background, screenshots, customer quotes._

## Design notes

_How we'll fix it._
