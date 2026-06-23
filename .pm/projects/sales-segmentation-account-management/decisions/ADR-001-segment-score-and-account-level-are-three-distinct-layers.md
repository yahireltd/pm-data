---
id: ADR-001
slug: segment-score-and-account-level-are-three-distinct-layers
title: Segment, Score and Account-Level are three distinct layers
state: rejected
decided: 2026-06-23
decided_by:
  - Austin
project: sales-segmentation-account-management
supersedes: null
superseded_by: null
tickets:
  - T-0456
  - T-0457
version: 2
---

## Context

Phase 2 kept stalling because three different ideas were tangled under the word "segment" — the 11/6 workshop "failed to agree details" arguing examples without a shared model. Reading the project + Ben's PDF made the tangle explicit.

## Decision

Treat them as **three distinct layers** that compose:

> **Segment (who they are) + Score/Value (how much they're worth) → Account Level (what we do) → Process / Ownership.**

- **Segment (A)** = identity: customer class, industry/sub-industry, event-types served, operational labels. *Descriptive; the thing to research.*
- **Score / Value (B)** = the 0–100 AI web-lookup score + A/B/C tier (T-0456 / TS-001) plus actual & potential value. *Largely built.*
- **Account Level (C)** = system / incubation / account-managed / strategic, and the routing to a conversion process (T-0457). *Derived from A + B via agreed thresholds.*

This maps 1:1 onto Ben's PDF spine — **Account Segmentation ≫ Engagement Model ≫ Workflow ≫ Ownership** — and his Scenarios 1–5 are each `(Segment + Value) → Level → Process`.

## Consequences

- Segmentation gets researched in its own right (it feeds scoring + level; it is not the same as either).
- T-0456 stays the score engine; T-0457 is really the *account-level/assignment* deliverable (re-scoped accordingly).
- MS-011 owns the segmentation taxonomy. Phase-2 milestone MS-002's mixed criteria now read against these three layers.
- For team ratification at the segmentation brainstorm (Ben owns the project).
