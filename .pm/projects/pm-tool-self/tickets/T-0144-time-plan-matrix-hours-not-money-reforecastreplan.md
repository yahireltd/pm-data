---
id: T-0144
title: Time-plan matrix (hours, not money) + reforecast/replan records
type: feature
state: done
priority: p2
created: 2026-05-29T13:28:10Z
updated: 2026-05-29T15:22:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 16384
reporter: null
assignee: null
acceptance_criteria:
  - A project carries a time-plan matrix expressed in hours (budgeted / current estimate / actual per cell) — never currency — surfaced in a web view
  - Reforecast and replan are captured as dated records with reasons, and a lint rule keeps the time plan well-formed
out_of_scope: []
code_anchors:
  - path: cli/src/types.ts
    symbol: TimePlan
    lines: 237-282
  - path: web/app/projects/[slug]/time-plan/page.tsx
  - path: web/app/_actions/timeplan.ts
  - path: linter/src/rules/time-plan.ts
    symbol: checkTimePlan
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
