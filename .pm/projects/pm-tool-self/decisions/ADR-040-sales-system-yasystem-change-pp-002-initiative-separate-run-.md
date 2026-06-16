---
id: ADR-040
slug: sales-system-yasystem-change-pp-002-initiative-separate-run-
title: "Sales = system (Yasystem) + change (PP-002 initiative): separate run from change, and link them"
state: proposed
decided: 2026-06-16
decided_by:
  - Austin
  - Ben
  - Zsolt
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0388
  - T-0389
  - T-0390
  - T-0391
  - T-0392
version: 1
---

> **Status: proposed — to be ratified at M-009 (17 Jun 2026).**

## Context

The Sales transformation is **both** an existing system and a large delivery programme, and the tool currently forces a single Project to be one `kind` (`system` *or* `initiative`):

- **Yasystem (P-0008, `kind: system`, repo `yahireltd/Ya-Hire-Management`)** is the live system we *run and maintain*. Sales is a department of it. It never ends.
- **PP-002 (Sales Segmentation / Account Management)** is the *change* — a gated, multi-milestone programme (kickoff + scoping already held; 6 scoped milestones; the Humanness "Sales Coherence" change journey).

There is no link between a system and the initiatives that change it, no grouping above a project, and no cross-project dependencies. Recent history shows the pull both ways: Logistics (P-0007) and the websites were *consolidated* into systems as streams/surfaces — right for code, but it would bury a tracked, high-stakes transformation as untracked BAU.

## Decision

1. **Separate run from change.** Keep **Yasystem as the system** (BAU, by department). **Convert PP-002 into an initiative** with the full guided lifecycle.
2. **The initiative ships into the system.** Point the initiative's `repo_url` at the same `Ya-Hire-Management` repo and carry Yasystem's guardrail ("ask before any push/commit to `master`").
3. **Phases = milestones/sprints inside the one initiative** — *not* a project per phase. A *separate* project only when a deliverable has an independent goal, different stakeholders, and a separable release (the already scoped-out items: Yamembership, franchise/comp design, logistics).
4. **Graduation.** When a milestone/slice clears hypercare → handover → its capability graduates to Yasystem BAU.
5. **Terminology.** Reserve "phase" for the lifecycle (intake→closure); delivery chunks are "milestones / slices".

## Consequences

New tool capabilities this implies (linked tickets):
- **T-0388** initiative → system link + graduation *(highest value — makes "both a system and a project" first-class)*
- **T-0389** cross-project dependencies / critical path
- **T-0390** programme rollup + projects→goals (the **coherence** aim)
- **T-0391** release / slice planning
- **T-0392** non-code / operating-model deliverables

Related demo needs (milestone-level governance): milestone ownership + stakeholders, and milestone meetings — tracked separately.

Trade-off accepted: the initiative and the system share one repo, so branch/stream and merge-window discipline matters (already noted in PP-002's scope re: the logistics stream).