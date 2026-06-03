---
id: ADR-003
slug: the-tool-must-push-hard-force-gates-bite-gates-che
title: "The tool must push hard: force gates bite, gates check substance, force-anyway leaves a scar"
state: accepted
decided: 2026-05-29
decided_by:
  - austin (human)
  - claude (agent)
project: pm-tool-self
supersedes: null
superseded_by: null
tickets: []
---

# ADR-003: The tool must push hard: force gates bite, gates check substance, force-anyway leaves a scar

## Context

A solo / two-person team has no peer pushback — there is no colleague to say "you skipped the
scoping meeting." So the tool itself **is** the external accountability. Austin explicitly wants
to be guided and, where it matters, forced into doing PM the right way rather than nudged with
optional hints he can ignore.

## Decision

- Promote the key lifecycle guides to **force gates** across Intake / Build / Ship / Hypercare.
- Gates check **substance, not mere existence**: e.g. a meeting must be `held_substantive`, a
  milestone must have non-empty acceptance criteria — a hollow placeholder does not pass.
- Any forced bypass requires a **typed reason** and is recorded **permanently** in
  `Project.phase_overrides` — a scar that stays on the record.
- **Nag** on stale status notes (STATUS001) so the weekly ritual does not silently lapse.

## Consequences

- Existing projects may now show as **blocked** until they meet the new gates — this is expected
  and correct, not a regression.
- Bypassing a gate is always possible but never free or invisible; the scar is the deterrent.
- The tool's opinion is now load-bearing; guidance that was advisory becomes enforcement.
