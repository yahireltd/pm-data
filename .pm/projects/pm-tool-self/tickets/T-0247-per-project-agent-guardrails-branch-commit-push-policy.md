---
id: T-0247
title: Per-project agent guardrails (branch + commit/push policy)
type: feature
state: done
created: 2026-06-05T21:04:45Z
updated: 2026-06-05T21:24:18Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Project carries an agent_policy (allow_commit, allow_push, optional note); unset = permissive (commit+push allowed) so new tools stay fast"
  - "[x] The agent's working branch + the commit/push policy are surfaced on pm_get_project / pm_get_ticket and reinforced by an AGENTS.md rule"
  - "[x] Hard claim-time gate: claiming a ticket in a locked project (commit or push disallowed) requires acknowledge_policy:true, else pm_claim_ticket throws explaining the policy"
  - "[x] Web project Overview can edit allow_commit/allow_push + note and shows a 'protected' badge when locked; stakeholders see it read-only"
  - "[x] Docs state production branches must also have GitHub branch protection (the hard git-level stop, since pm-tool is not in the git path)"
out_of_scope: []
code_anchors:
  - path: cli/src/types.ts
    symbol: AgentPolicy
  - path: schemas/project.schema.json
    symbol: agent_policy
  - path: cli/src/lib/agent-policy.ts
  - path: mcp-server/src/lib/agent-policy.ts
  - path: mcp-server/src/tools/claim-ticket.ts
  - path: mcp-server/src/tools/get-project.ts
  - path: mcp-server/src/tools/get-ticket.ts
  - path: web/app/_actions/projects.ts
    symbol: setAgentPolicy
  - path: web/app/_components/AgentPolicyEditor.tsx
  - path: web/app/projects/[slug]/overview/page.tsx
  - path: AGENTS.md
    symbol: §12
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2117
    model: claude-opus-4-8
    started: 2026-06-05T21:17:11Z
    status: completed
    ended: 2026-06-05T21:17:31Z
    summary: "Each project can now set rules for what an AI agent is allowed to do in its code: which branch to work on, and whether the agent may commit and/or push. New, fast-moving tools stay open by default (so we can build quickly); production projects — like a live site's main branch — get locked down on purpose. The project's Overview has simple \"Allow commit / Allow push\" toggles plus a note for the agent, and shows a clear \"Protected\" badge when locked. The agent is shown these rules when it picks up work, and if a project is protected it has to explicitly acknowledge the limits before it can start — on a no-push project it prepares the change and hands it to a person instead of pushing. Because the tool itself can't physically stop git, production branches should also be protected on GitHub (which hard-blocks a push regardless); the tool makes the policy clear and points at the right branch."
    test_plan: "On a project Overview, toggle Allow commit / Allow push + add a note → saves; a 'Protected — no push' badge shows when locked; stakeholders see it read-only. pm_get_project / pm_get_ticket return the effective policy (verified: pm-tool-self shows allow_commit/allow_push true, locked false). Lock a project (allow_push:false) and have an agent pm_claim_ticket without acknowledge_policy → it's refused with the policy explained; with acknowledge_policy:true → it claims and the run records the acknowledgement (the MCP arg needs a client reconnect to appear). A permissive project claims with no acknowledgement (verified). Production branches additionally carry GitHub branch protection for the hard stop."
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: ADR-033 records the enforcement model; AGENTS.md §12 + dev-wiki item 12 state the agent rule; new agent_policy field documented in the project schema JSON.
labels: []
attention: null
---

## Problem

Agents should work only on a project's selected branch, with per-project commit/push permissions — loose on fast-moving new tools (build fast), strict on production like the yasystem `main` project (be careful). Today a project carries `repo_url` + `branch` (T-0243) but no commit/push policy, and nothing reminds or constrains an agent.

## Context

pm-tool is not in the git path, so it **declares + surfaces** the policy and **hard-gates at claim** (the one git-adjacent action it controls), plus an AGENTS.md rule agents follow — the same trust model as the close-the-loop records gate. The unbypassable git-level stop for production is **GitHub branch protection**, which pm-tool points you at via the project's branch. Default is permissive; production projects are locked down explicitly.

See ADR (to be filed) + the plan. Builds on T-0243 (repo_url + branch) and ADR-009 (RBAC).

## Conversation

**2026-06-05 21:17 claude:** Run run-20260605-2117 completed — Each project can now set rules for what an AI agent is allowed to do in its code: which branch to work on, and whether the agent may commit and/or push. New, fast-moving tools stay open by default (so we can build quickly); production projects — like a live site's main branch — get locked down on purpose. The project's Overview has simple "Allow commit / Allow push" toggles plus a note for the agent, and shows a clear "Protected" badge when locked. The agent is shown these rules when it picks up work, and if a project is protected it has to explicitly acknowledge the limits before it can start — on a no-push project it prepares the change and hands it to a person instead of pushing. Because the tool itself can't physically stop git, production branches should also be protected on GitHub (which hard-blocks a push regardless); the tool makes the policy clear and points at the right branch.

---

**2026-06-05 21:18 — you**

all done

**2026-06-05 21:24 claude:** **What we did** — We added per-project rules that control what an AI assistant is allowed to do in a project's code, including which working area it uses and whether it may save or publish changes. Each project's Overview now has simple on/off switches plus a note, and shows a clear "Protected" badge when a project is locked down.

**Why** — Some projects move fast and benefit from staying open, while important ones (like a live website) need to be handled carefully. Until now there was no way to set those expectations per project, and nothing reminded or limited the assistant.

**What would have happened if we did nothing** — The assistant could make and publish changes to sensitive projects without any guardrails, and people would have to rely on memory and manual care to keep that from going wrong.

**The benefit** — Teams can now decide, project by project, how much freedom the assistant has. Fast projects stay quick, while protected projects make the assistant pause and get explicit confirmation before acting (and on locked projects it prepares the change and hands it to a person instead). This helps both the people building quickly and the people protecting important, live work.
