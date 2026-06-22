---
id: T-0192
title: "Commit-msg hook + pm reconcile: nudge a T-NNNN per commit, surface untracked shipped work"
type: feature
state: triaged
created: 2026-06-03T22:23:12Z
updated: 2026-06-22T16:31:40Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
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
collaborators:
  - kind: human
    name: Austin Pickering
version: 4
---

## Problem
Work ships in git but the project record doesn't see it: in the 2026-06-02/03 push ~13 commits referenced no ticket and several deep working sessions went uncaptured. ADR-003 says the tool must force the record to match reality — but it can't see git at the point of work.
## Proposal
- A commit-msg git hook that warns/blocks a commit with no `T-NNNN` (or an explicit `[no-ticket]` override).
- `pm reconcile`: list commits since the last sync with no ticket reference; optionally map merged commits → tickets to catch the 'shipped to prod but never run through the loop' class.
## Context
Realises the git/merged-commit reconciliation explicitly deferred in T-0176 (commit ba49c1c). Dogfood: surfaced by reconciling the 2026-06-02/03 work against the record.

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep check, re-confirmed) — unbuilt; build SECOND.**

No commit-msg hook and no `pm reconcile` exist (the only hook is a pre-commit `.pm`-markdown linter, and it isn't even installed). Good news — it's splittable and de-risked: the commit-msg hook is a ~30-line sibling of the existing `linter/hooks/pre-commit` reusing the `T-NNNN` regex already in `linter/src/lib/git.ts`; `pm reconcile` is the larger half but the existing `commitsReferencing()` helper does most of the ticket→commit mapping. One thing to define: what "since the last sync" means (a `--since` flag, a tag, or a stored timestamp) — there's no sync marker today. Problem's live: 4 of the last 12 commits carry no ticket id. Establishes the hook-install pattern T-0395 reuses.

---

**2026-06-22 16:31 — Austin Pickering**

we want to allow but be aware not fully block the commit
