---
id: T-0389
title: "Cross-project dependencies: blocks/blocked-by between projects + programme critical path"
type: feature
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
Dependencies can only be expressed between *tickets*, not *projects*. A programme (the Sales initiative plus its carve-outs — Yamembership, logistics) has cross-project sequencing the tool can't represent: e.g. the Yamembership go/no-go gates a downstream build; the logistics merge-window must be coordinated with Sales.

## Proposal
- Allow a project/initiative to declare `blocks` / `blocked_by` against another project.
- Surface a dependency / critical-path view across a programme.

## Context
M-009 design review. Falls out of treating large work as several linked projects rather than one.

## Acceptance criteria
- A project can declare blocks/blocked_by another project.
- The dependency is visible from both sides.
- (Stretch) a sequence / critical-path view across linked projects.