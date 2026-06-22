---
id: T-0451
title: "Project-level deploy contract (default: do-not-deploy) — move deploy out of the universally-served AGENTS.md §9"
type: feature
state: triaged
created: 2026-06-22T17:16:14Z
updated: 2026-06-22T17:16:14Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - Projects carry a deploy contract (likely on agent_policy); every existing project defaults to an explicit do-not-deploy
  - pm_get_project and pm_get_ticket return the deploy contract to agents; a web editor can set it
  - AGENTS.md §9 no longer presents pm-tool's deploy as universal — it points to the per-project contract and defaults to do-not-deploy/hand-off-to-human
  - pm-tool-self's own deploy contract captures its real process (pm-deploy + typecheck-before-build); the pm-tool-specific mechanics live in docs/development.md, not the broadcast conventions
  - An agent reading a project with no/which-says-do-not-deploy contract is clearly told NOT to deploy
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - agent-experience
  - agent-policy
  - safety
  - dogfood-find
attention: null
version: 1
---

## Problem

AGENTS.md §9 "The deploy contract" hardcodes **pm-tool-self's own** deploy (`pm-deploy`, `/opt/pm-tool/code`, `restart pm-tool pm-mcp`). The other 11 sections are general "how to use pm-tool" rules that apply to any agent on any project — but §9 is project-specific.

**T-0330 (just shipped) now serves the whole AGENTS.md to every agent connecting to the live MCP**, regardless of which project they're working on. So an agent managing yasystem, a website, or the stock engine now receives pm-tool's deploy contract as if it were universal. That's a hazard, not just noise: a different project deploys differently (or shouldn't be deployed by an agent at all), and an agent could read "push to master → `pm-deploy` → restart pm-tool" and apply it to the wrong project.

## Proposal (Austin, 2026-06-22)

Define the deploy contract **at the project level, inside the tool** — a natural sibling of the existing per-project `agent_policy` (allow_commit / allow_push / branch / locked).

- Add a per-project **deploy contract**: how THIS project deploys, or an explicit "do not deploy".
- **Default = do-not-deploy for ALL existing projects** (safe migration). An agent must not deploy a project unless its contract says how.
- Surface it via `pm_get_project` + `pm_get_ticket` (alongside agent_policy) and a web editor (charter / agent-policy panel).
- **Reframe AGENTS.md §9** to a general principle: deploy is per-project — read the project's deploy contract; if it's unset/"do not deploy", hand off to a human and never deploy. pm-tool-self's own deploy mechanics move to `docs/development.md` (or become pm-tool-self's deploy-contract value).

## Design notes / open questions

- **Where it lives:** extend `agent_policy` (e.g. `agent_policy.deploy`) vs a separate `project.deploy_contract`. agent_policy already gates commit/push and is returned to agents — leaning that way.
- **Shape:** free-text instructions vs structured (command + branch + restart targets). Free-text is simplest for v1; "do-not-deploy" is a first-class state, not an empty string.
- **Migration:** stamp every existing project to do-not-deploy; pm-tool-self gets its real contract (pm-deploy + the typecheck-before-build gate from T-0265).
- Relates: **T-0330** (surfaced this by broadcasting AGENTS.md), **T-0265** (its §9 typecheck-gate detail is pm-tool-self-specific and should move here / to docs). (Can't set the `relates` field over MCP yet — that's literally **T-0267**.)

## Interim mitigation (separate, cheap)

Until this ships, neutralise the live hazard by reframing AGENTS.md §9 so it no longer reads as universal deploy instructions — lead with "deploy is per-project; default is DO NOT deploy unless your project defines one; the following is pm-tool's own."