---
id: T-0161
title: Risk-review freshness lint (RISK001) — warn when an open risk wasn't reviewed in the latest status note
type: chore
state: done
priority: p2
created: 2026-06-02T15:16:41Z
updated: 2026-06-02T00:00:00Z
project: pm-tool-self
section: null
parent: null
children: []
order: 32768
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - RISK001 warns when an OPEN risk has no reviewed_at, or one older than the project's latest status note
  - A risk reviewed at/after the latest status note does not warn; closed/realised risks are exempt
  - The rule is registered and runs in pm-lint; lint stays green on real .pm except intended warnings
out_of_scope: []
code_anchors:
  - path: linter/src/rules/risk-review.ts
  - path: linter/src/index.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
backlog_status: in_review
source: charter
---
# T-0161: Risk-review freshness lint

## Problem

Course big-rocks sublist: 'Risk not reviewed in last status warns.' The risk register exists (project.risks[] with reviewed_at) but no lint enforces freshness. Add a warn-level rule (RISK001): for each open risk, if reviewed_at predates the most recent status note's date, warn — so risk review stays a live ritual, not a write-once field.

## Acceptance criteria

_To define at sprint promotion._
