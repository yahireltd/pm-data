---
id: T-0364
title: "Branch protocol gap: resolved branch vs actual checkout in shared working trees (agent worked off-branch on T-0363 without a recorded deviation)"
type: chore
state: triaged
created: 2026-06-12T02:59:38Z
updated: 2026-06-12T02:59:38Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - AGENTS.md defines what an agent must do when the claim-resolved branch differs from the repo's current checkout (especially shared checkouts carrying another agent's stream)
  - The deviation, when allowed, must be recorded ON the ticket (comment and/or a field), not only in the run test_plan
  - pm_complete_run (or pm_record_run_progress) can carry where the work physically landed (e.g. actual branch / untracked-in-tree) so the review banner shows it
  - T-0363 retro-annotated as the worked example
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/claim-ticket.ts
    symbol: branch resolution returned to agents
  - path: AGENTS.md
    symbol: section 12 working-branch rules
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - agents-md
  - branch-on-ticket
attention: null
version: 1
---

## Problem

During T-0363 (yasystem sales-scores view), pm_claim_ticket resolved the working branch as `master` (project default) and the agent acknowledged the locked no-commit policy. But the developer's single shared checkout of the repo was sitting on `refund-hardening-t0331-stripe-dryrun` — another in-flight stream. The agent (correctly, arguably) did not switch the shared checkout's branch, produced new untracked files only, and deferred the commit to the human per policy. However: nothing in the protocol covers this case, and the deviation was only mentioned in the run's test_plan prose — the ticket record still says branch=master as if the work happened there.

Austin's verdict: "the tool didn't follow protocol or protocol was wrong" — both are half-true, which is the bug.

## What's missing

1. **AGENTS.md rule for the mismatch case.** Proposed: when resolved branch ≠ current checkout in a shared tree, the agent must (a) prefer a separate `git worktree` for the resolved branch, or (b) if the output is new untracked files only and commits are human-gated anyway, proceed WITHOUT switching but post a ticket comment recording where the work physically lives and what branch it is destined for. Never flip a shared checkout's branch.
2. **A place to record it structurally.** The ticket `branch` field means "the stream this work belongs to" (T-0352) — it can't express "untracked in tree, destined for master, checkout was on X". Options: an optional `landed` note on pm_complete_run artifacts, or convention: a `**Where the work lives:**` line in the closing comment.
3. **Claim-time nudge.** pm_claim_ticket can't see local state, but its response could carry one line: "verify your checkout matches this branch; if you can't switch (shared tree), record the deviation on the ticket."

## Worked example

T-0363: 3 new files written into the Ya-Hire-Management tree while it sat on refund-hardening-t0331-stripe-dryrun; deployed to test box; commit to master deferred to Austin. Files are untracked so they follow any checkout — benign in this instance, but only by luck of being new-files-only.