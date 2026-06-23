---
id: ADR-009
slug: reconcile-retire-the-segmentation-strawman-lineage-v5-is-can
title: Reconcile & retire the segmentation strawman lineage — v5 is canon (ADR-001 + ADR-007 + ADR-008); surviving governance restated
state: proposed
decided: 2026-06-23
decided_by: []
project: sales-segmentation-account-management
supersedes: ADR-003
superseded_by: null
tickets:
  - T-0473
  - T-0474
version: 1
---

## Context
The segmentation taxonomy was developed iteratively in one session (ADR-002 → ADR-006), then validated against the full 528-customer base, producing ADR-007 (taxonomy v5) and ADR-008 (scoring v5). Several early ADRs now describe superseded thinking, so the team needs one clear record of what is canon and what is retired.

**Tooling note:** the live MCP cannot safely flip an existing ADR's `state` to `superseded` — `pm_get_decision`/`pm_update_decision` are not project-scoped and would risk other projects' identically-numbered ADRs (filed as a pm-tool bug). This ADR therefore declares the supersession *in writing*; the per-record `state` flips follow once that bug is fixed.

## Decision
**Canon (current):**
- **ADR-001** — Segment / Score / Account-Level are three distinct layers. Foundational, unchanged.
- **ADR-007** — Segment taxonomy v5, validated at 528 (3-level Industry → company_type → sub_type, + place-of-worship type, peer/competitor label, venue_hire_model, derived high-potential, closed sub-type vocab).
- **ADR-008** — Scoring v5 baseline + variance causes + rubric fixes.

**Retired / superseded:**
- **ADR-002** (data model: controlled-vocab industry + review queue) → superseded by ADR-003, now by this ADR.
- **ADR-003** (data model "corrected": *company_type as the industry base*) → its **structural claim is overturned** by ADR-006/007 (Industry is its OWN level *above* company_type). Its **governance survives** (below). Formally superseded by this ADR.
- **ADR-004** (taxonomy v0 strawman: 4 dimensions incl. "class") → "class" was replaced by Industry (+ Personal flag, derived role); superseded by ADR-005 → 006 → 007.
- **ADR-005** (vocabulary v0.2: class/industry/event-type tables) → tables replaced by the validated v5 vocabulary in ADR-007; its "coverage-by-construction" goal was met (528/528). Superseded by ADR-007.
- **ADR-006** (de-conflate Industry from company_type — 3 levels) → correct, and **extended** by ADR-007 (already linked).

**Surviving governance (carried forward from ADR-002/003):** segment vocabularies are **closed / controlled** with a **review queue** — the scrape proposes new company_type/sub_type via `company_type_proposed`; additions are human-reviewed before entering the enum, never auto-created. This governs the build in T-0474.

## Consequences
- The strawman set (ADR-002/004/005, and ADR-003's structural half) is retired; ADR-006 is extended by ADR-007.
- Their `state` fields still read `proposed` until the pm-tool project-scoping bug is fixed and they can be flipped to `superseded` safely — at which point this ADR is the authority for which becomes what.
- Canon for the team to react to = ADR-001 + ADR-007 + ADR-008 (+ this reconciliation), all `proposed` pending Ben's confirmation (taxonomy ownership).
- T-0473 (finalise vocabulary) and T-0474 (one-pass scrape + vocab tables + review queue) inherit the surviving governance.