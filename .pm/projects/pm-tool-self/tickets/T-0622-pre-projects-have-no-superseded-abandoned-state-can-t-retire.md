---
id: T-0622
title: Pre-projects have no "superseded/abandoned" state — can't retire a duplicate or replaced idea
type: feature
state: triaged
created: 2026-07-17T22:18:25Z
updated: 2026-07-17T22:18:25Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Pre-project supports a terminal 'superseded' (and/or 'abandoned') state, separate from draft/planning/ready/converted
  - Optional superseded_by link to another PP-NNN, surfaced in the UI
  - pm_update_pre_project accepts the new state; pm_list_pre_projects excludes retired ones by default (with a filter to include them)
  - Web UI shows retired pre-projects as retired, with a link to the replacement
  - PP-005 re-marked superseded_by PP-013 via the new mechanism (text stopgap removed)
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/pre-project-tools.ts
    line: 195
    note: state enum ["draft","planning","ready"] (+converted) — add superseded/abandoned + superseded_by field
  - path: web/app/_lib/types.ts
    note: pre-project state type + list filtering
  - path: web/app/_actions/pre-projects.ts
    note: web actions / list query to exclude retired
  - path: linter/src/rules/frontmatter.ts
    note: pre-project frontmatter validation of state
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem
A pre-project can only be draft → planning → ready → converted. There's no terminal state for an idea that's been dropped, replaced, or merged into another. So when a newer pre-project supersedes an older one, the old one just lingers in the active list looking live.

## Context (found by dogfooding, 2026-07-17)
PP-005 ("Make this tool useable for the masses / Project Jeevs", 6 Jun) is the same idea as the newer PP-013 (white-label the whole tool). Austin wanted to mark PP-005 as superseded and couldn't — no state for it. Marked in text as a stopgap; this ticket is the real fix. Tickets already have this concept (duplicate / wontfix) — pre-projects should mirror it.

## Proposal
- Add a terminal state — `superseded` (and/or `abandoned`) — distinct from draft/planning/ready/converted.
- Optional `superseded_by: PP-NNN` pointer (like a ticket's duplicate link) so the replacement is discoverable.
- Exclude retired pre-projects from active lists by default; show them retired (badged/greyed) with the pointer.
- Then re-mark PP-005 → superseded_by PP-013 via the real mechanism, replacing the text stopgap.