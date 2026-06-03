---
id: T-0131
title: Fix Intake-skip bug; add cost_of_inaction + phase_overrides fields
type: bug
state: done
priority: p2
created: 2026-05-29T13:28:01Z
updated: 2026-05-29T13:28:16Z
project: pm-tool-self
section: null
parent: null
children: []
order: 3072
reporter: null
assignee: null
acceptance_criteria:
  - Project creation no longer skips the Intake phase — the intake-skip bug is fixed so new projects start at Intake
  - Project schema gains cost_of_inaction and phase_overrides fields, accepted on create and validated by the schema
out_of_scope: []
code_anchors:
  - path: cli/src/commands/new.ts
  - path: schemas/project.schema.json
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
