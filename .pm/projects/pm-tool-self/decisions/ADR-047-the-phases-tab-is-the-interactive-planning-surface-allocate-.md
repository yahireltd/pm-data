---
id: ADR-047
slug: the-phases-tab-is-the-interactive-planning-surface-allocate-
title: The Phases tab is the interactive planning surface; allocate at every level; sprints start empty
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
  - T-0423
version: 1
---

## Context

A read-only Phases rollup wasn't enough — you need to *build* the plan, not just view it. Two views (a read-only Phases tree + a flat Milestones list) duplicated the hierarchy, and the milestone list didn't show phase grouping. Tickets had no way to see or change which project / phase / milestone / sprint they belonged to.

## Decision

Make the **Phases tab THE interactive planning surface** (and the first tab): build the plan top-to-bottom inline — create a phase, file a milestone under a phase (a phase combobox = the grouping control), add a sprint, allocate a ticket (commit an existing one OR scope a new one), set phase + sprint owners, delete a sprint. On each **ticket**, a **Project → Phase → Milestone → Sprint cascade** where each picker narrows the next.

- **"Add sprint" creates an EMPTY sprint** (you then allocate tickets explicitly) rather than auto-seeding a ticket per milestone criterion — the auto-seed produced junk tickets (criteria lines like "Goal:…" / "Outcome:…") and trapped the user with sprints they couldn't delete.
- **Demo/example data carries an `[example]` prefix** so it is trivially removable.
- The change-set was put through an automated **adversarial multi-agent review** before being trusted; the medium findings were fixed.

## Consequences

- **+** One place to plan top-to-bottom; explicit, predictable allocation at every rung; the ticket and the plan stay in sync.
- **+** Closed the no-delete-sprint gap (T-0423) as part of it.
- **−** More interactive UI to maintain; the first free-text comboboxes had stale-value + silent-failure bugs (caught by the review; fixed by making them controlled + validated + reset-on-no-match).
- For id-resolving fields (the ticket cascade) we chose constrained **selects** over free text for robustness.