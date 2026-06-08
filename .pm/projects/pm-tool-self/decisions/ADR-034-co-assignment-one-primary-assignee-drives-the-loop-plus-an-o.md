---
id: ADR-034
slug: co-assignment-one-primary-assignee-drives-the-loop-plus-an-o
title: "Co-assignment: one primary assignee drives the loop, plus an optional collaborators list"
state: accepted
decided: 2026-06-08
decided_by:
  - austin
  - claude
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0262
---

## Context

A ticket can only name a single `assignee` today. But real work often has more than one person on it — e.g. Austin with his agent and Zsolt with his agent on related slices at the same time (T-0262). We want to show "a human *and* an agent are both on this" without breaking the machinery that assumes one owner.

The single `assignee` is load-bearing: `pm_claim_ticket` / `claimWork` set it, the close-the-loop run state keys off it, collision-safe claiming (T-0169, ADR-?) guards it, and dashboards/lint read it. Turning it into an array would ripple through all of that.

## Decision

Keep `assignee` as the single **primary** owner — the one the claim/run/close loop and collision guard continue to use, unchanged. Add an optional **`collaborators: Actor[]`** alongside it: additional people or agents "also working on this", of any `kind` (human/agent/customer/system). Collaborators are descriptive only — they do not claim, do not open agent runs, and do not affect the state machine, lint, or the records gate.

Display-wise, strengthen the shared `Avatar` primitive so human vs agent vs stakeholder is obvious at a glance (kind glyph + ring + bolder name), since it's the single component every who/whom surface renders through — satisfying the ticket's "stronger icons and fonts anywhere we display user and stakeholders" in one place.

## Consequences

- Non-breaking: `collaborators` is optional; everything that reads `assignee` is untouched.
- The primary assignee still answers "who owns this / who do I chase". Collaborators answer "who else is on it".
- If we later need true multi-owner semantics (multiple claims/runs), that's a separate, larger decision — this deliberately avoids it.
- A small risk of the two fields drifting (someone in `collaborators` who's really the owner); mitigated by keeping the editor simple and the primary always first.