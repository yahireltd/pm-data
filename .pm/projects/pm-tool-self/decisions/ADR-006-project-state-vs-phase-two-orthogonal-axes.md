---
id: ADR-006
slug: project-state-vs-phase-two-orthogonal-axes
title: Project state vs phase — two orthogonal axes
state: proposed
decided: 2026-06-02
decided_by: []
project: pm-tool-self
supersedes: null
superseded_by: null
tickets: []
---

# ADR-006: Project state vs phase — two orthogonal axes

## Context

The lifecycle audit flagged confusion between `project.state` and `project.phase`. They are not the same axis and must not be collapsed.

## Decision

Keep both, orthogonal:

- **phase** (intake → planning → build → test → ship → hypercare → closure): WHERE the project sits in the guided lifecycle — the process stage, advanced via the gated Advance control.
- **state** (planning | active | paused | done | cancelled): the project's WORKFLOW status — being actively worked, paused, finished, or dropped.

A project is typically `active` across most phases. On reaching the `closure` phase with its force gates met, the human clicks **Close project**, which sets `state = "done"` — the bridge between the two axes. The effective close timestamp is `closure.recorded_at` (there is no separate `closed_at`; the project schema is `additionalProperties: false`).

## Consequences

- The Phase tab's Advance control drives `phase`; the new Close button drives `state` (only at closure, gates met).
- The dashboard "Ready to close" signal = phase==closure && force gates met && state != done.
- Possible follow-up: a lint warning when state/phase are inconsistent (e.g. state=done but phase != closure).
