---
id: ADR-042
slug: enforce-docs-travel-with-the-change-8-with-a-gate-not-a-norm
title: Enforce "docs travel with the change" (§8) with a gate, not a norm
state: proposed
decided: 2026-06-16
decided_by:
  - Austin
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context

AGENTS.md §8 requires docs (SCHEMA.md, the in-app dev wiki at `/docs` → `web/app/docs/content.ts`, and the user help at `/help` → `web/app/help/content/`) to be updated in the *same change* as the code. It is a written norm with no enforcement. A large feature session (milestone governance, sprint-from-milestone, new MCP tools) shipped with **none** of those updated — the norm was forgotten under load.

This is the **same failure mode** as the close-the-loop problem ([[project_close_loop_forcing_function]]): work finishes, the record doesn't. A rule that relies on the agent remembering fails exactly when it matters most. The tool's whole philosophy is that gates must bite — but we don't apply it to the tool's own docs. "It keeps happening" because nothing enforces it.

## Decision

Turn §8 from a norm into a **gate**.

- **Docs-drift gate in the build/lint:** if a change touches `schemas/`, `cli/src/types.ts`, or `mcp-server/src/tools/` without a matching change to `SCHEMA.md` / `web/app/docs/content.ts` / `web/app/help/content/`, the build flags it (warn first, then fail). The deploy build is already the type-gate; this makes it a docs-gate too — stale docs can't ship.
- **Earlier nudge (optional):** a Claude Code stop/commit hook runs the same check at commit time, before the build.

## Consequences

- §8 becomes enforced like the type-gate and the close-the-loop gate — independent of discipline.
- The check is a proxy: it verifies the doc was *touched*, not that it's *correct* — but it forces the agent to confront docs at the moment of change, which is the failure point. (A `# docs-ok: <reason>` escape hatch handles genuine no-doc-needed changes, leaving a scar like force-anyway.)
- The pattern generalises: other soft AGENTS.md rules can be backfilled into gates the same way.
- Builds on the existing close-the-loop docs attestation (which fires at ticket-done); this catches work that isn't funnelled through a ticket.