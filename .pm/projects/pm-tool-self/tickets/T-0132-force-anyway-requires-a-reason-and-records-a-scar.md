---
id: T-0132
title: Force-anyway requires a reason and records a scar; add STATUS001 status-cadence lint rule
type: feature
state: done
priority: p2
created: 2026-05-29T13:28:01Z
updated: 2026-05-29T13:28:16Z
project: pm-tool-self
section: null
parent: null
children: []
order: 4096
reporter: null
assignee: null
acceptance_criteria:
  - Using "force anyway" to bypass a phase gate requires a non-empty reason and records a durable scar/override note on the project
  - A STATUS001 status-cadence lint rule flags items whose status notes fall outside the expected cadence
out_of_scope: []
code_anchors:
  - path: cli/src/lib/phase-override.ts
  - path: linter/src/rules/status-cadence.ts
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
