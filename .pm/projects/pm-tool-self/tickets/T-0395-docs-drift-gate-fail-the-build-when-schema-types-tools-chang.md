---
id: T-0395
title: "Docs-drift gate: fail the build when schema/types/tools change without a docs update (§8 enforcement)"
type: feature
state: triaged
created: 2026-06-16T19:53:44Z
updated: 2026-06-22T16:16:29Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - A change that edits a schema/type/tool (schemas/, cli/src/types.ts, mcp-server/src/tools/) without a matching doc edit (SCHEMA.md / web/app/docs/content.ts / web/app/help/content/) is flagged by the lint/build
  - "An explicit escape hatch (e.g. a docs-ok: <reason> marker) lets a genuine no-docs-needed change through, recording the reason"
  - Wired into the deploy build (the existing type-gate) so it actually blocks; warn-first then promote to hard fail
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
version: 5
collaborators:
  - kind: human
    name: Austin Pickering
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

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep check, re-confirmed) — unbuilt; build THIRD (after T-0265 + T-0192).**

§8 "docs travel with the change" is still pure prose, no gate anywhere. **Scope correction:** the existing pm-lint linter is the *wrong* host — it only lints `.pm/` data markdown, so this needs a **new source-diff check**, not a new lint rule. Sequence it after **T-0265** (which gives it a real pre-deploy gate to hook into) and reuse **T-0192's** commit-hook + git-diff plumbing rather than rebuilding. Note: the agent-run `docs_note` field is self-reported, not an enforced gate — don't mistake it for partial work.
