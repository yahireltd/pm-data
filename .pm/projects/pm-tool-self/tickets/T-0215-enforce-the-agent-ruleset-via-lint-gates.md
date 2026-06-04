---
id: T-0215
title: Enforce the agent ruleset via lint gates
type: feature
state: review
created: 2026-06-04T02:58:50Z
updated: 2026-06-04T03:16:14Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260604-0316
    started: 2026-06-04T03:16:14Z
    status: completed
    ended: 2026-06-04T03:16:14Z
    summary: "Three warn-level lint gates enforce the detectable AGENTS.md rules: MEET006 (held meeting with no outcomes), RISK002 (active project past intake with a stale/absent status note), VERIFY001 (done ticket with unticked acceptance criteria). The non-detectable rules stay documented-only."
    test_plan: Run pm-lint across the data repo -> the three rules flag existing violations as warnings (not errors); nothing breaks.
labels:
  - linter
  - hygiene
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-04T03:16:14Z
---

## Problem
The AGENTS.md ruleset (ADR-007) is documented guidance, not enforced — an agent can ignore it. Per the tool's "force-gates bite / hard guidance" philosophy (ADR-003), the mechanically-detectable rules should become lint gates that surface violations.

## Scope
Add lint rules for the parts a linter can actually detect. The non-detectable rules (record a decision, log a tech-session, keep docs/help updated) stay documented-only — a linter cannot detect "a decision was made and not written down."

## Conversation

**2026-06-04 03:16 claude-code:** Run run-20260604-0316 completed — Three warn-level lint gates enforce the detectable AGENTS.md rules: MEET006 (held meeting with no outcomes), RISK002 (active project past intake with a stale/absent status note), VERIFY001 (done ticket with unticked acceptance criteria). The non-detectable rules stay documented-only.
