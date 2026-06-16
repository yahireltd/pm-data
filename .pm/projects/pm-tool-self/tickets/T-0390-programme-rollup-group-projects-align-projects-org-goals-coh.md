---
id: T-0390
title: "Programme rollup: group projects + align projects → org goals (coherence)"
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
  - coherence
  - modeling
attention: null
version: 1
---

## Problem
There is no way to group multiple projects under one programme/portfolio, or to align a project to a named org goal — `aligns_with[]` exists but is an empty placeholder, and `parent_project` is an unimplemented skeleton. When the Sales transformation grows beyond one initiative, there's no combined view; and there's no projects→goals alignment, which is the company's stated **coherence** aim (people/agents/projects/goals).

## Proposal
- Implement `parent_project` rollup (or a first-class `programme` grouping) with a combined view.
- Populate `aligns_with` against named org goals; add a goal → projects coherence view.

## Context
M-009. Defer the build until a second Sales initiative exists, but capture it now as the coherence feature — it's the through-line to the org's #1 aim.

## Acceptance criteria
- Multiple projects roll up under one programme/parent with a combined view.
- A project can align to one or more named org goals.
- (Stretch) a goal → projects coherence view.