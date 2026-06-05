---
id: T-0247
title: Per-project agent guardrails (branch + commit/push policy)
type: feature
state: triaged
created: 2026-06-05T21:04:45Z
updated: 2026-06-05T21:04:45Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - Project carries an agent_policy (allow_commit, allow_push, optional note); unset = permissive (commit+push allowed) so new tools stay fast
  - The agent's working branch + the commit/push policy are surfaced on pm_get_project / pm_get_ticket and reinforced by an AGENTS.md rule
  - "Hard claim-time gate: claiming a ticket in a locked project (commit or push disallowed) requires acknowledge_policy:true, else pm_claim_ticket throws explaining the policy"
  - Web project Overview can edit allow_commit/allow_push + note and shows a 'protected' badge when locked; stakeholders see it read-only
  - Docs state production branches must also have GitHub branch protection (the hard git-level stop, since pm-tool is not in the git path)
out_of_scope: []
code_anchors: []
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

Agents should work only on a project's selected branch, with per-project commit/push permissions — loose on fast-moving new tools (build fast), strict on production like the yasystem `main` project (be careful). Today a project carries `repo_url` + `branch` (T-0243) but no commit/push policy, and nothing reminds or constrains an agent.

## Context

pm-tool is not in the git path, so it **declares + surfaces** the policy and **hard-gates at claim** (the one git-adjacent action it controls), plus an AGENTS.md rule agents follow — the same trust model as the close-the-loop records gate. The unbypassable git-level stop for production is **GitHub branch protection**, which pm-tool points you at via the project's branch. Default is permissive; production projects are locked down explicitly.

See ADR (to be filed) + the plan. Builds on T-0243 (repo_url + branch) and ADR-009 (RBAC).