---
id: T-0260
title: Rule for agent to require permission. to mutate project data
type: feature
state: ready
priority: p2
created: 2026-06-05T22:13:08Z
updated: 2026-06-08T18:05:14Z
project: pm-tool-self
section: null
parent: null
children: []
order: 82944
reporter: null
assignee: null
acceptance_criteria:
  - new option where we have git permissions for this
  - agents need to have this rule enforced if set
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2214
    model: claude
    started: 2026-06-05T22:14:00Z
    status: abandoned
    summary: "Reconciling an orphaned claim. Claimed via the web UI on 5 June, never worked (no progress, no code). More to the point, what it asks for — a per-project control over whether agents may commit/change a project's data, enforced when set — has already been delivered by T-0247 (per-project agent guardrails: branch + commit/push policy) and decision ADR-033. Recommend closing this as a duplicate of T-0247 rather than building it again; pulling it out of limbo so that call can be made."
    ended: 2026-06-08T18:04:49Z
labels: []
attention:
  needed_by: human
  reason: "Agent run abandoned: Reconciling an orphaned claim. Claimed via the web UI on 5 June, never worked (no progress, no code). More to the point, what it asks for — a per-project control over whether agents may commit/change a project's data, enforced when set — has already been deli"
  since: 2026-06-08T18:04:49Z
---

Seems like a good idea to have this so we can be fully in control or allow agent freedom

## Conversation

**2026-06-08 18:05 claude:** **Chased down — recommend closing as a duplicate of T-0247.**

This was claimed on 5 June and never worked (no progress, no code). But the thing it asks for already exists: **T-0247 — "Per-project agent guardrails (branch + commit/push policy)"** (shipped, commit 19eda46) and decision **ADR-033** deliver exactly this — each project carries an `agent_policy` (`allow_commit` / `allow_push` / branch), and an agent must acknowledge that policy to claim a ticket in a project that restricts it (the claim-gate).

**Recommendation:** close T-0260 as **duplicate of T-0247** (or wontfix) rather than rebuild it. I've taken it out of "in progress"; it's now `ready` + flagged for you. If you think there's a gap T-0247 *doesn't* cover (e.g. controlling data mutations through the MCP specifically, separate from git commits), say so and I'll re-scope it as a fresh, concrete ticket — note that the MCP-side authz is itself part of T-0257 in the new sprint.
