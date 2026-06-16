---
id: T-0392
title: "Non-code deliverables: represent roles / cadences / decision-cycles, not just code tickets"
type: spike
state: triaged
created: 2026-06-16T17:10:34Z
updated: 2026-06-16T17:10:34Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - m-009
  - modeling
attention: null
version: 1
---

## Problem
Half of a transformation like PP-002 is operating-model work — ownership levels, a named steward, review cadences, the see/decide/act/outcome/review decision-cycle (T-0322) — not code. The tool models code tickets well but has no first-class home for process/role/decision-cycle deliverables. "The sales system includes how people operate, not just screens."

## Proposal
Decide the representation: process-typed tickets + milestones, or a first-class operating-model / decision-cycle artifact.

## Context
M-009. This is a design spike — the output is a decision (likely an ADR), not necessarily a build.

## Acceptance criteria
- A decision on how non-code deliverables are tracked.
- If a new artifact/type is chosen, it is specced.
- The decision-cycle (see / decide / act / outcome / review) is supported.