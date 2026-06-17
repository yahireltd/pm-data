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
version: 10
---

# ADR-039: Project taxonomy: systems vs initiatives; branches live on tickets, surfaces on system projects

## Context

Two unlike things currently share the "project" shape:

- **Delivery initiatives** — need planning, phases, gates, and an end.
- **Long-lived systems under upkeep** — the ERP, the websites — which never end.

Today's markers are cosmetic: `category:"system"` only feeds the Dev Tickets view, and `in_development` only moves a sidebar group. Systems get phase-gate nagging they shouldn't ("Projects that need you" treats an upkeep system stuck at intake as blocked forever); initiatives can quietly dodge the lifecycle by looking like upkeep.

ADR-031 modelled each system as a real project flagged `category:"system"` — that model STANDS; this ADR replaces only its flag mechanism (`category` → `kind`, with `category` kept as a legacy read-time input).

Two real-world distortions forced the issue:

1. **Branch in the project name.** The ERP (Ya-Hire-Management) is one repo with several concurrent feature branches, but branch is a single project-level value — so the branch ended up in the project NAME ("Yasystem Main Branch"), and there's no way to tell an agent "this ticket is the refund-hardening stream". A project per branch is wrong: branches die at merge and would orphan their ticket history.
2. **One repo, many sites.** Yasite is ONE repo with folders per site (yahirenew, chl, frontend, backend, hirecatering), but the tool forces one repo+branch per project, so the sites got smeared across three half-empty projects.

A dormant `code_surfaces: string[]` field exists on the project schema — written as `[]` at five creation sites, read nowhere.

## Decision

Adopt a three-part taxonomy, each part its own ticket:

**1. Project `kind: "initiative" | "system"` (T-0350 — SHIPPED 2026-06-11; sidebar/treatment follow-up T-0358 — SHIPPED 2026-06-11).** The split is behavioural: guided lifecycle (phase health, force-gates, "Projects that need you", Ready to close) applies to initiatives only; the sidebar groups Systems / Initiatives / Closed purely by kind + state (legacy `in_development` and the meta label no longer affect placement); systems' nav drops Phase/Charter/Milestones/Time plan; the Dev Tickets `category:"system"` notion is reconciled to ONE notion. **Defaulting is derived at read time** via `projectKind()` (cli/src/lib/project-kind.ts) — no bulk migration. Create/promote/convert paths stamp kind explicitly.

**2. Surfaces on projects (T-0351 — SHIPPED 2026-06-11).** Project gains `surfaces: [{ key, name, path?, repo_url?, branch? }]` — named sub-areas defaulting to the project's repo/branch plus a path. Ticket gains optional `surface` (a key), validated at the write paths; `pm_claim_ticket`/`pm_get_ticket` return the resolved surface. Surface tags are project-scoped and are CLEARED on cross-project moves (a same-key surface in the destination would silently mis-resolve). **`code_surfaces` is superseded**: kept in the schema for legacy files, creation sites stopped initialising it.

**3. Branch lives on the ticket (T-0352 — SHIPPED 2026-06-12).** Ticket gains optional `branch`. Effective branch = `ticket.branch ?? parent ticket's (sub-tasks inherit, resolved at read time; the walk stops at project boundaries — branch names are repo-scoped) ?? surface.branch ?? project.branch`, implemented once (`effectiveBranch()`, cli/src/lib/surfaces.ts) and returned by claim/get together with the agent policy. Pinned branches SURVIVE cross-project moves (that's how a stream folds into its system). If unset and the project default is protected, the agent's convention (AGENTS.md §1) is to cut `t<NNNN>-<slug>` and record it back — the stream survives the branch's deletion.

**Settled (was the open point):** when a ticket/surface pins a branch on a LOCKED project, **the pinned branch wins** as the working branch — pinning is a human act at scoping; the lock's commit/push limits apply to it unchanged. Recorded in AGENTS.md §12.

**Sequencing:** T-0350 → T-0351 → T-0352 (all shipped), then the data migration (T-0353: Websites (yasite) as the one website system — Austin configured P-0015 with four surfaces; fold Logistics-rollout into Yasystem with pinned stream branches; rename Yasystem).

**Deferred:** per-branch / per-surface agent policy (one policy per project stands); automated branch cleanup/merge detection; linter status-cadence exemption for systems.

## Consequences

- Work stops being lost when a branch ends: tickets, runs, and the recorded branch name stay on the long-lived project. Agents stop guessing which branch or folder a stream belongs to — claim hands them folder + repo + branch.
- Systems escape phase-gate nagging and the delivery machinery; initiatives can no longer dodge the lifecycle — dashboard "needs you" signals become trustworthy.
- The branch leaves the project NAME (it's data now); the sidebar speaks the taxonomy (Systems / Initiatives / Closed).
- Costs honoured in implementation: all category read-sites go through `projectKind()`; surface integrity is enforced at write paths and moves clear the tag; the parent-branch walk is bounded, cycle-guarded, and project-contained; the resolution helpers live in cli/src/lib and are IMPORTED by mcp-server (not mirrored).
