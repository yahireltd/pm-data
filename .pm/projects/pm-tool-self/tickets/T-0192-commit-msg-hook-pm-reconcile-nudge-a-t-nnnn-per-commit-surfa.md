---
id: T-0192
title: "Commit-msg hook + pm reconcile: nudge a T-NNNN per commit, surface untracked shipped work"
type: feature
state: triaged
created: 2026-06-03T22:23:12Z
updated: 2026-06-03T22:23:12Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - A commit without a T-NNNN (or explicit [no-ticket] override) is flagged at commit time via a git hook
  - "`pm reconcile` lists commits since the last sync that carry no ticket reference"
  - Optionally maps merged commits to their tickets to catch 'shipped but never closed' work
out_of_scope: []
code_anchors:
  - path: linter/src
    note: pre-commit/commit-msg hook lives here
  - path: cli/src/commands
    note: pm reconcile command
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - dx
  - backfill
attention: null
---

## Problem
Work ships in git but the project record doesn't see it: in the 2026-06-02/03 push ~13 commits referenced no ticket and several deep working sessions went uncaptured. ADR-003 says the tool must force the record to match reality — but it can't see git at the point of work.
## Proposal
- A commit-msg git hook that warns/blocks a commit with no `T-NNNN` (or an explicit `[no-ticket]` override).
- `pm reconcile`: list commits since the last sync with no ticket reference; optionally map merged commits → tickets to catch the 'shipped to prod but never run through the loop' class.
## Context
Realises the git/merged-commit reconciliation explicitly deferred in T-0176 (commit ba49c1c). Dogfood: surfaced by reconciling the 2026-06-02/03 work against the record.