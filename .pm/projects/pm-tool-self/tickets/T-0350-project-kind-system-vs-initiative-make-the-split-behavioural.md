---
id: T-0350
title: "Project kind: system vs initiative — make the split behavioural (ADR-039)"
type: feature
state: triaged
created: 2026-06-10T14:59:23Z
updated: 2026-06-10T14:59:23Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - Project schema + types carry kind (initiative|system); existing data defaults correctly without manual edits (category:system → system)
  - Dashboard phase-health sections (Projects that need you / Ready to close) skip system-kind projects entirely
  - Sidebar Existing vs In Development grouping derives from kind (in_development honoured as legacy override, not required)
  - Kind is editable from the project Overview and settable on create/promote
  - Dev Tickets view and kind agree — one notion of 'system', not two parallel flags
out_of_scope:
  - Surfaces (separate ticket)
  - Ticket-level branch (separate ticket)
  - Per-branch agent policy (deferred per ADR-039)
code_anchors:
  - path: schemas/project.schema.json
  - path: cli/src/types.ts
    symbol: Project
  - path: web/app/page.tsx
    symbol: projectStats / projectsNeedingYou — phase health currently runs for every active project
  - path: web/app/_components/Sidebar.tsx
    symbol: devSlugs — in_development||meta grouping
  - path: web/app/_components/ProjectStageControl.tsx
  - path: web/app/dev-tickets/page.tsx
    symbol: category===system filter
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - taxonomy
attention: null
version: 1
---

## Problem

Two unlike things share the "project" shape: delivery initiatives (need planning, phases, gates, an end) and long-lived systems under upkeep (the ERP, the websites — never end). Today's markers are cosmetic: `category:"system"` only feeds the Dev Tickets view, and `in_development` only moves a sidebar group. Systems get phase-gate nagging they shouldn't ("Projects that need you" treats an upkeep system stuck at intake as blocked forever); initiatives can quietly dodge the lifecycle by looking like upkeep.

## Design (ADR-039)

Add an explicit `kind: "initiative" | "system"` to the project schema (defaulting sensibly from today's data: `category:"system"` → system, else initiative).

Behaviour:
- Guided lifecycle (phase health, force-gates, "Projects that need you", Ready to close) applies to **initiatives only**; systems are exempt.
- Sidebar grouping derives from kind: systems → "Existing Projects", initiatives → "Projects In Development". `in_development` becomes a derived/legacy override, no longer the source of truth.
- Dev Tickets view keeps listing `category:"system"`-style projects; reconcile that with kind so there's one notion, not two.
- Kind is editable on the project Overview (like the existing stage control), and settable at creation/promote time.

## Out of band

Surfaces and ticket-level branches are separate tickets (this one is just the type split).