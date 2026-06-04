---
id: ADR-028
slug: meetings-capture-suggested-features-as-gradable-convertible-
title: Meetings capture suggested features as gradable, convertible scope items
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0201
---

## Context
Scoping and stakeholder meetings surface 'we should also build X' ideas. Left in free-text minutes they are invisible to planning; promoted straight to tickets they bypass triage and inflate scope. We needed a place for raw suggestions that is structured enough to triage but separate from committed work.

## Decision
Add a `suggested_features[]` array to the Meeting schema: each entry holds the suggestion text, an optional `grade`/priority assigned during triage, and a link set once it is converted to a feature ticket. Suggestions are captured on the meeting first, graded, and only then converted — mirroring how meeting outcomes promote to follow-up tickets.

## Consequences
Meetings gain another sub-array and a small triage step before an idea becomes a ticket. In exchange, mid-project scope suggestions are visible and rankable at their source rather than lost in prose or silently absorbed, and conversion to a feature ticket is an explicit, traceable act that supports scope-creep visibility.