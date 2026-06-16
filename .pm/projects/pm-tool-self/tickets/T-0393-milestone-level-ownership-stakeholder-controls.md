---
id: T-0393
title: Milestone-level ownership + stakeholder controls
type: feature
state: triaged
created: 2026-06-16T17:16:35Z
updated: 2026-06-16T17:16:35Z
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
  - milestones
  - sales
attention: null
version: 1
---

## Problem
Milestones (MS-) carry only title / target_date / acceptance_criteria / state / slip_records — **no owner and no stakeholders**. For the Sales initiative we want governance *at the milestone level*: who owns this milestone, and who the stakeholders on it are (a milestone is where a phase's accountability actually lives).

## Proposal
- Add `owner` (actor) and `stakeholders[]` to the milestone schema.
- Surface them in CLI/MCP create + update and the web milestone detail/list.
- Reuse the existing stakeholder controls and the project-register people picker (T-0385) so it's consistent with projects/tickets.

## Context
M-009 / Sales demo — Austin wants milestone-level ownership visible when demoing the phased structure.

## Acceptance criteria
- A milestone can have an owner and a stakeholder list.
- Both are settable via the web UI and MCP.
- Owner + stakeholders show on the milestone list and detail.
- Reuses the existing stakeholder / register picker.