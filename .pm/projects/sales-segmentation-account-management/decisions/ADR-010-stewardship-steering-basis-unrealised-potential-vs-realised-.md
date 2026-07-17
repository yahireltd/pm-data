---
id: ADR-010
slug: stewardship-steering-basis-unrealised-potential-vs-realised-
title: Stewardship steering basis — unrealised potential vs realised value (needs sales-expert input)
state: rejected
decided: 2026-07-17
decided_by: []
project: sales-segmentation-account-management
supersedes: null
superseded_by: null
tickets:
  - T-0457
  - T-0479
version: 2
---

## Context

When confirming Scoring v5 as the baseline (M-008 decision 2, recorded 2026-07-03), Ben flagged an unresolved question one layer up: **what should steer stewardship-level assignment — unrealised potential or realised value?**

The current design carries both:
- The confidence-weighted blend (`expected_value = α·realised_blend + (1−α)·potential`, blend addendum) steers by a mix, while **banding gates on realised-£ floors** (levels are earned by money, never by score alone).
- The **Defend | Grow lens** prototype (commit 9c45b3ce1) shows exactly this tension as a UI toggle: the same four levels banded by realised (defend what we have) vs by potential (grow what we could have).

Chasing unrealised potential prioritises white-whales and growth accounts (risk: senior time on customers who never convert). Protecting realised value prioritises defending existing revenue (risk: the PDF's core complaint — big new opportunities under-served — persists).

## Decision

OPEN — needs a sales-expert opinion (Nathan / Sam are the named Sales SMEs) before the threshold workshop can settle final banding. Candidate resolutions: (a) blend as designed, α curve tuned at workshop; (b) realised-only banding with potential as a watch-overlay (Strategic-watch); (c) dual-lens operating mode (Defend|Grow) with ownership rules per lens.

## Consequences

Blocks final ratification of the T-0457 banding rule and the T-0479 parameter defaults (α curve, potential weights). Does NOT block the scoring baseline (ADR-008, accepted), the taxonomy (ADR-007, accepted), or shadow-mode operation of the engine — thresholds and weights remain tunable pending this call.
