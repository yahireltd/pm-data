---
id: TS-018
slug: phase-delivery-planning-layer-design-build-16h
title: Phase delivery-planning layer — design → build (≈16h)
project: pm-tool-self
created: 2026-06-23T01:31:42Z
updated: 2026-06-23T01:33:52Z
decisions:
  - 'Add a Phase delivery layer (PH-NNN) between project and milestone, with the milestone→phase link and a Project→Phase→Milestone→Sprint→Ticket rollup — see ADR-045. Naming: new entity = "Phase", existing lifecycle "Phase" relabelled "Lifecycle", code uses phase_id/phases to dodge the collision.'
  - Ownership ladder — the human PM owns the project/phase, the DEV owns the sprint, the AGENT owns the ticket; added sprint.owner and a per-dev roadmap that aggregates a dev's owned sprints. See ADR-046.
  - "The Phases tab is THE interactive planning surface (first tab): add/allocate at every level inline, plus a Project→Phase→Milestone→Sprint cascade on each ticket; demo data is marked with an [example] prefix so it's removable. See ADR-047."
actions:
  - text: Retire or repurpose the now-redundant MS-002 "Phase 2 — Sales OS Foundation" umbrella milestone (superseded by PH-02 + the six deliverable milestones).
    ticket: T-0458
  - text: Clear the 8 low-severity findings from the adversarial review (sprint-delete should warn it un-commits tickets; a rollup double-counts a cross-milestone ticket; add stale-version basedOnVersion guards to the new phase/sprint write actions).
    ticket: T-0458
  - text: Relabel the lifecycle "Phase" wording inside the PhaseCommandCenter page to "Lifecycle" (only the tab was relabelled), and write the docs for the new entities (sprint.owner + Phase in SCHEMA.md, dev wiki, help).
    ticket: T-0458
side_quests:
  - "An early \"plan sprint\" auto-seeded a ticket per milestone criterion, creating junk sprints/tickets the user couldn't delete (criteria lines like \"Goal:…\"/\"Outcome:…\"). Cleaned them up and reversed the behaviour: \"add sprint\" now creates an empty sprint."
  - "There was no way to delete a sprint at all (the T-0423 gap) — built it mid-session: removeSprint web action + pm_delete_sprint MCP tool (confirm:true) + a planner trash control."
  - "Ran an adversarial multi-agent review of the whole planner change-set: 18 confirmed findings (7 medium, 11 low). Fixed the mediums same-session — the free-text comboboxes were going stale after refresh and silently swallowing unmatched text; made them controlled + validated. The 11 lows are parked on T-0458."
problem: "Planning a phased initiative (the Sales programme — four phases) had nowhere structural to live: \"phases\" were just words typed into milestone titles, the real `phase` field is the lifecycle enum (intake→ship), and a phase that needs several milestones couldn't be expressed, owned, or rolled up. There was also no way to see a plan from the big picture down to a dev's actual sprint of work, no dev-level roadmap, and no way to delete a sprint or to see/change where a ticket sits in the hierarchy."
success_criterion: The tool can plan a phased initiative top-to-bottom — Project → Phase → Milestone → Sprint → Ticket — interactively, with ownership at the right level at each rung and a per-dev roadmap; and the Sales programme is structured for real on it.
phase: build
version: 4
---

# TS-018: Phase delivery-planning layer — design → build (≈16h)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
