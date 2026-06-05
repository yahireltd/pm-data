---
id: ADR-033
slug: per-project-agent-guardrails-declarative-policy-hard-claim-g
title: "Per-project agent guardrails: declarative policy + hard claim-gate; GitHub branch protection is the hard git stop"
state: accepted
decided: 2026-06-05
decided_by:
  - Austin
  - claude
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0247
---

## Context

Agents should work only on a project's selected branch, with per-project commit/push permissions — loose on fast-moving new tools, strict on production (e.g. a live site's `main`). pm-tool is a PM/data tool + MCP interface; it has zero git execution, so it cannot physically block a commit or push.

## Decision

Add an optional per-project `agent_policy` (`allow_commit`, `allow_push`, `note`); **unset = permissive** so new tools stay fast, and production projects are locked down explicitly. The required working branch is the project's existing `branch`. Enforcement is layered:

1. **Surface** the effective policy on `pm_get_project` / `pm_get_ticket` so a claiming agent sees branch + commit/push up front.
2. **Hard claim-time gate**: `pm_claim_ticket` refuses to claim a *locked* project (commit or push disallowed) unless `acknowledge_policy: true` is passed; the acknowledgement is recorded as a `policy_ack` on the run.
3. **AGENTS.md §12**: the rule agents follow — work only on the branch; commit/push only if allowed; on a locked project, hand off to a human / open a PR instead of pushing.
4. **GitHub branch protection** is the unbypassable hard stop for production branches (configured on GitHub, aligned with the project's `branch`). pm-tool points you at the branch but does not configure it.

The shared rule lives once (`cli/src/lib/agent-policy.ts` + an MCP mirror) so it can't drift — same approach as the close-the-loop records gate.

## Consequences

- pm-tool's layer is **trust + audit** (declared policy, surfaced everywhere, acknowledged at claim) — not a sandbox. A determined or out-of-band `git push` is only stopped by GitHub branch protection, which production repos must therefore have.
- Default-permissive means a brand-new project is open until someone locks it — intentional, to keep new builds fast.
- The new `acknowledge_policy` MCP arg needs an MCP client reconnect to appear in the tool schema (the server-side gate is active immediately).
- Builds on T-0243 (repo_url + branch) and ADR-009 (RBAC). Fields are additive/optional, so existing projects are unaffected.