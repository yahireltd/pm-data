---
id: ADR-039
slug: project-taxonomy-systems-vs-initiatives-branches-live-on-tic
title: "Project taxonomy: systems vs initiatives; branches live on tickets, surfaces on system projects"
state: proposed
decided: 2026-06-10
decided_by: []
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0350
  - T-0351
  - T-0352
  - T-0353
version: 2
---

# ADR-039: Project taxonomy: systems vs initiatives; branches live on tickets, surfaces on system projects

## Context

Two unlike things currently share the "project" shape:

- **Delivery initiatives** — need planning, phases, gates, and an end.
- **Long-lived systems under upkeep** — the ERP, the websites — which never end.

Today's markers are cosmetic: `category:"system"` only feeds the Dev Tickets view, and `in_development` only moves a sidebar group. Systems get phase-gate nagging they shouldn't ("Projects that need you" treats an upkeep system stuck at intake as blocked forever); initiatives can quietly dodge the lifecycle by looking like upkeep.

Two real-world distortions forced the issue:

1. **Branch in the project name.** The ERP (Ya-Hire-Management) is one repo with several concurrent feature branches, but branch is a single project-level value — so the branch ended up in the project NAME ("Yasystem Main Branch"), and there's no way to tell an agent "this ticket is the refund-hardening stream". A project per branch is wrong: branches die at merge and would orphan their ticket history.
2. **One repo, many sites.** Yasite is ONE repo with folders per site (yahirenew, chl, frontend, backend, hirecatering), but the tool forces one repo+branch per project, so the sites got smeared across three half-empty projects.

A dormant `code_surfaces: string[]` field exists on the project schema — written as `[]` at five creation sites, read nowhere.

## Decision

Adopt a three-part taxonomy, each part its own ticket:

**1. Project `kind: "initiative" | "system"` (T-0350).** The split becomes behavioural: guided lifecycle (phase health, force-gates, "Projects that need you", Ready to close) applies to initiatives only; sidebar grouping derives from kind (`in_development` honoured as legacy override, no longer the source of truth); the Dev Tickets view's `category:"system"` notion is reconciled so there is ONE notion of system. **Defaulting is derived at read time** via a single shared helper — `kind ?? (category === "system" ? "system" : "initiative")` — applied at every category read-site; no bulk file migration (avoids version-bump conflicts with open tabs).

**2. Surfaces on projects (T-0351).** Project gains `surfaces: [{ key, name, path?, repo_url?, branch? }]` — named sub-areas that default to the project's repo/branch plus a path. Ticket gains optional `surface` (a surface key); `pm_claim_ticket` returns the resolved surface so agents work in the right folder without guessing. **`code_surfaces` is superseded**, not grown: it stays in the schema as deprecated (existing `[]` values remain lint-valid), creation sites stop initialising it, and `surfaces` is the real field.

**3. Branch lives on the ticket (T-0352).** Ticket gains optional `branch`, pinned by a human at scoping. Effective branch at claim = `ticket.branch ?? surface.branch ?? project.branch`, returned explicitly by `pm_claim_ticket`. If unset AND the project default is protected, the agent's convention (AGENTS.md §1) is to cut a ticket-named branch (`t<NNNN>-<slug>`) and record it back on the ticket — the stream survives the branch's deletion. **Sub-task inheritance is resolved at claim time** by walking `ticket.parent` (one implementation), not copied at creation (three creation paths that would go stale).

**Sequencing:** T-0350 → T-0351 → T-0352, then the data migration (T-0353: one Yasite system with surfaces; Yasystem drops "Main Branch" from its name). Schemas have `additionalProperties: false`, so the updated schemas deploy BEFORE any data carries the new fields.

**Open point to arbitrate in T-0352:** when `ticket.branch` differs from a LOCKED project's `agent_policy` branch, which wins — must be decided before the AGENTS.md convention is written.

**Deferred:** per-branch / per-surface agent policy (one policy per project stands); automated branch cleanup/merge detection.

## Consequences

- Work stops being lost when a branch ends: tickets, runs, and the recorded branch name stay on the long-lived project. Agents stop guessing which branch or folder a stream belongs to.
- Systems escape phase-gate nagging; initiatives can no longer dodge the lifecycle — dashboard "needs you" signals become trustworthy.
- The branch leaves the project NAME (it's data now); the sidebar shows one website project instead of three shells.
- Cost: ~6 category read-sites must go through the shared kind helper (a missed site silently recreates the two-notions-of-system bug); claim-ticket's response shape grows (consumers will start depending on effective branch/surface); the resolution helper should live in cli/src/lib and be IMPORTED by mcp-server (precedent: complete-run.ts), not hand-mirrored a third time.
- All three tickets touch cli/src/types.ts, and T-0351/T-0352 both restructure claim-ticket.ts and update-ticket.ts — the sequencing above touches each hot spot once.
