---
id: T-0130
title: Promote lifecycle guides to real force gates + substance checks
type: feature
state: done
priority: p2
created: 2026-05-29T13:28:00Z
updated: 2026-05-29T13:28:16Z
project: pm-tool-self
section: null
parent: null
children: []
order: 2048
reporter: null
assignee: null
acceptance_criteria:
  - Lifecycle phase guides are enforced as real gates — advancing a phase is blocked unless the prior phase's required artifacts are present and non-empty (not just suggested)
  - Gate checks validate substance, not mere presence — placeholder/empty fields fail the gate rather than passing it
out_of_scope: []
code_anchors:
  - path: cli/src/lib/playbook.ts
  - path: .pm/playbook.yml
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
