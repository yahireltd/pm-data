---
id: T-0268
title: Richer review handoff — surface what changed + what to test inline at review
type: feature
state: triaged
created: 2026-06-05T22:53:16Z
updated: 2026-06-05T22:53:16Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - At review, the human sees the change summary + a what-to-test checklist inline (not just a state badge)
  - Clear confirm vs send-back-with-a-comment
  - Reduces the manual "paste a test plan + comment" an agent does today
out_of_scope: []
code_anchors: []
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

When an agent finishes (→ review), the human’s job is "confirm or send back" but the change summary + test steps aren’t front-and-centre (the run’s test_plan exists but is buried). Surfacing them inline makes the human’s verification fast + reliable.

## Context

Relates to T-0252 (risk-scaled rigor). Surfaced dogfooding on 2026-06-05.