---
id: T-0145
title: ADR template variants (dependency/integration/deployment/change-control) + lint + multi-lens reviews
type: feature
state: done
priority: p2
created: 2026-05-29T13:28:10Z
updated: 2026-05-29T15:22:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 17408
reporter: null
assignee: null
acceptance_criteria:
  - Decisions support typed ADR variants (dependency / integration / deployment / change-control) with variant-specific required fields validated by a lint rule
  - Each variant requires sign-off across its relevant review lenses before the ADR can be accepted, enforced by a multi-lens review lint rule
out_of_scope: []
code_anchors:
  - path: schemas/decision.schema.json
  - path: web/app/_actions/decisions.ts
  - path: linter/src/rules/adr-variant.ts
    symbol: checkAdrVariant
  - path: linter/src/rules/adr-review.ts
    symbol: checkAdrReview
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
