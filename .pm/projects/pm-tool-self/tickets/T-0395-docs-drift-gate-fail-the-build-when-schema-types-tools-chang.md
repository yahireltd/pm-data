---
id: T-0395
title: "Docs-drift gate: fail the build when schema/types/tools change without a docs update (§8 enforcement)"
type: feature
state: triaged
created: 2026-06-16T19:53:44Z
updated: 2026-06-16T19:53:44Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - forcing-function
  - docs
  - dx
attention: null
version: 1
---

## Problem
AGENTS.md §8 ("docs travel WITH the change") is a norm with no teeth, so it gets forgotten under load — a whole feature session shipped with SCHEMA.md, the `/docs` dev wiki, and the `/help` user guide all left stale. Same root cause as the close-the-loop gap: rules that depend on the agent remembering fail. See ADR (this project).

## Proposal
Add a docs-drift check to the lint/deploy build:
- If a commit/diff touches `schemas/`, `cli/src/types.ts`, or `mcp-server/src/tools/` AND does NOT touch `SCHEMA.md` / `web/app/docs/content.ts` / `web/app/help/content/`, flag it.
- Warn first (a few weeks), then promote to a hard fail so stale docs can't deploy.
- Provide an explicit escape hatch — e.g. a `docs-ok: <reason>` marker in the commit/PR — that records WHY no doc was needed (a scar, like force-anyway).
- Optional companion: a Claude Code stop/commit hook running the same check earlier.

## Acceptance criteria
- A change that edits a schema/type/tool without a matching doc edit is flagged by the lint/build.
- An explicit escape hatch lets a genuine no-docs-needed change through, recording the reason.
- Wired into the deploy build (the existing type-gate) so it actually blocks.