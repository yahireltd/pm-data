---
id: T-0215
title: Enforce the agent ruleset via lint gates
type: feature
state: triaged
created: 2026-06-04T02:58:50Z
updated: 2026-06-04T02:58:50Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - "Lint rule: a meeting in state 'held' with no recorded outcomes is flagged (AGENTS.md §4)"
  - "Lint rule: an active project past the intake phase whose latest status note is stale (>7d) or absent is flagged, mirroring RISK001 (AGENTS.md §6)"
  - "Lint rule: a ticket in state 'done' whose acceptance_criteria are not all ticked ('[x] ') is flagged as closed-without-verify (AGENTS.md §1)"
  - Rules follow the existing linter rule pattern + registry; severities are warn (not error) so existing data isn't broken; non-detectable rules remain documented-only
out_of_scope: []
code_anchors:
  - path: linter/src/rules/
    note: new rules, mirror RISK001 / ATRISK001 / MEET rules
  - path: linter/src/lib/collect.ts
    note: entity loading
  - path: AGENTS.md
    note: the rules being enforced
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - linter
  - hygiene
attention: null
---

## Problem
The AGENTS.md ruleset (ADR-007) is documented guidance, not enforced — an agent can ignore it. Per the tool's "force-gates bite / hard guidance" philosophy (ADR-003), the mechanically-detectable rules should become lint gates that surface violations.

## Scope
Add lint rules for the parts a linter can actually detect. The non-detectable rules (record a decision, log a tech-session, keep docs/help updated) stay documented-only — a linter cannot detect "a decision was made and not written down."