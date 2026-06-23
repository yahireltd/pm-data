---
id: ADR-045
slug: add-a-phase-delivery-layer-ph-nnn-between-project-and-milest
title: Add a Phase delivery layer (PH-NNN) between project and milestone
state: accepted
decided: 2026-06-23
decided_by:
  - Austin
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0458
  - T-0391
version: 1
---

## Context

A phased initiative (the Sales programme = four phases) had nowhere structural for its phases. Teams jammed "Phase N" into milestone *titles*; the existing `phase` field is the **lifecycle** enum (intake→ship), not a delivery phase. A phase that needs several milestones couldn't be grouped, owned, sequenced, or rolled up. The M-009 modeling cluster (T-0388/390/391/392) had circled this without resolving it.

## Decision

Introduce a first-class **Phase entity (PH-NNN)** between a project/initiative and its milestones. Fields: goal, owner, stakeholders, start_date + target_date, entry_gate, depends_on[] (phase sequencing), state. Milestones link up via the optional **`milestone.phase_id`**. Hierarchy becomes `Project → Phase → Milestone → Sprint → Ticket`; a phase's progress rolls up from its milestones → sprints → tickets.

**Naming (the load-bearing call):** keep "Phase" as the user-facing name for the *new delivery entity*; relabel the existing lifecycle "Phase" to **"Lifecycle"**; in code use `phase_id` / `phases` to avoid colliding with the existing `phase` enum, `pm phase` command and `/phase` route. The entity absorbs T-0391 (ordered + gated slices = `order` + `entry_gate` + `depends_on`).

## Consequences

- **+** A real grouping layer: phases own milestones, the plan is navigable top-to-bottom, progress rolls up. Phase 2 of Sales decomposed from one overloaded milestone (8 criteria) into six trackable deliverable milestones.
- **−** A new entity across schema / cli / mcp / web (mirrors Milestone).
- **−** "Phase" is now overloaded with the lifecycle stage — mitigated by the Lifecycle relabel (tab done; in-page text relabel still pending).
- **−** `milestone.phase_id` can dangle — now validated on write (web + a follow-up for MCP).

**Alternatives rejected:** milestone-as-phase (recreates the overloaded-milestone problem); a grouping field/label only (no rollup, no sequencing/gating); programme-above-project (T-0390 — orthogonal, deferred).