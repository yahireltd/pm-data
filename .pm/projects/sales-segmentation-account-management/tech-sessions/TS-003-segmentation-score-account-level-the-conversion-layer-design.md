---
id: TS-003
slug: segmentation-score-account-level-the-conversion-layer-design
title: "Segmentation+score → account-level: the conversion-layer design (two-track grid + suggest→confirm machine)"
project: sales-segmentation-account-management
created: 2026-06-26T13:45:54Z
updated: 2026-06-26T13:47:11Z
decisions:
  - THREE LAYERS STAY DISTINCT. Segment (who they are) / Score (how good a prospect — potential, blind to spend) / Account-level (how we steward them). Account-level is the missing third layer and is NEVER silently derived from the other two. It also sits BESIDE — never replaces — the existing tierID (Diamond/Gold/Silver/Bronze value-grade) and accountType (Cash/Credit billing).
  - "THE ENGINE = a two-track Value×Potential grid, both axes in £/yr. POTENTIAL (X) = a static, anti-leakage curve from the score (never moved by spend). ACTUAL (Y) = realised hire-only revenue from ya_contracts. Levels: low actual+low potential → System-only (default floor); low actual+high potential → Incubation (the ~311 white-whale nursery); high actual → Account; high actual+high potential+repeat cadence → Strategic. Movement is VERTICAL (realised value climbs toward the customer's own fixed potential ceiling); share-of-wallet = actual÷potential; white-whale gap = vertical distance to the diagonal. This resolves Ben's 'move toward top-right' — it's 'up toward the ceiling', not lateral."
  - "NEW vs EXISTING is the two-track split. EXISTING (has REALISED hire history — a delivered contract, not just a future-dated booking) runs the full ladder on realised value. NEW (potential only) collapses onto the X-axis and is HARD-CAPPED at Incubation — a customer cannot be born Account/Strategic, they GRADUATE as real revenue accrues. Key fix: a forward-dated booking does NOT flip a new customer to 'existing' (that defeated the cap); it fires committed_fwd → big-fish alert + senior fast-track ownership NOW, but the durable level stays Incubation until revenue is realised. The single £60k one-off wedding routes to the 'Lifetime' follow-up process, NOT Strategic (Strategic now requires txn_count_12m >= 2 repeat cadence)."
  - "THE CONVERSION = a suggest→confirm state machine, not an algorithm that decides. System writes suggested_level only; a human sets confirmed_level (effective_level = COALESCE(confirmed, suggested)). States: suggested → proposed → owner_assigned → requirements_met → confirmed (+ rejected/demote). Each level has an ownership GATE before it is 'real': System-only → named pool steward (no individual salesID); Incubation → customerType-aware data capture (Corporate resolves companyType; Personal/no-domain uses the same set MINUS companyType so it can actually be satisfied); Account → semi-bespoke plan + manager approval; Strategic → bespoke plan + manager sign-off + a HARD per-senior-AM capacity cap (because ~311 candidates ≫ headcount). Transfer windows = quarterly review worklist (suggested≠confirmed, lapsed high-tier, split-ownership, disproven-whale). Big-fish = real-time manager alert on arrival."
  - "SEGMENT (95% missing) MODULATES, NEVER GATES a level — no rule depends on companyType, so the gap can't stall an assignment; segment is a fit-multiplier on potential (unknown ×1.0, never penalise missing data) and a Strategic tie-breaker. Enrichment is on-demand: resolving companyType becomes a requirement of the Incubation gate, so the engine itself becomes the segment-backfill engine. COMPETITORS are NOT excluded: a new customer_sales_scores.classification ENUM('mainstream','trade','disqualified') routes trade/sub-hire peers to a trade lane (System-only + trade pricing, or Account if high-volume) regardless of history; only genuinely not-events businesses are disqualified to System-only. GRAIN: the unit of stewardship is the economic account = email domain, rolled onto a deterministic 'home row' (merge primary, else highest-lifetime same-domain row) to avoid N-fold inflation across same-domain contacts; ownership conflicts (≥2 AMs on one domain) are FLAGGED, never silently overwritten."
actions:
  - text: "Full design doc (10 sections: model, level-assignment logic, new-vs-existing, 95%-missing-segment, conversion workflow, data model/schema, build sequencing, risks, 18 open decisions, edge cases) saved locally at ~/Documents/P-0018-account-level-design.md (design-only project — not committed). Produced via a 4-design panel + adversarial critique + red-team pass; top approach = Two-Track Scoring Engine (8/10)."
  - text: "WORKSHOP: take the design doc to the team and settle the 18 open decisions — most blocking are the £ thresholds (from a real percentile study), the Strategic per-senior-AM capacity cap number, the named SYSTEM/trade pool steward (+ who funds the Phase-4 nurture engine it depends on), and the domain-ownership cascade rule (who wins when 2 AMs already own contacts at one agency). These gate Phase 2."
    ticket: T-0457
  - text: "BUILD (post-approval) per section 7: Phase 0 = add customer_sales_scores.classification + seed from funnel flags (T-0456), indexed ya_customers.email_domain, roleID→seniority stub. Phase 1 MVP = customer_account_levels + log tables, nightly recompute job (hire-only contractTotal, domain-rolled to home row, committed_fwd + pending_quote_value, NULL-safe momentum, status_stage, white-whale, big-fish, hysteresis), my-accounts level badge + confirm/override + audit, Strategic RBAC + capacity cap. Phase 2 = full state machine + gates + transfer-window job + parameterised big-fish mailer. Phase 3 = enrichment-on-demand (T-0473/4/5). Phase 4 = the (currently unbuilt) nurture engine."
    ticket: T-0457
side_quests: []
problem: "We have an initial scoring model (web-lookup, blind to spend, A/B/C) and an initial segmentation (industry) model, but no designed way to get a customer from there to ONE of the four account levels (System / Incubation / Account / Strategic), nor a process for how Sales actually CONVERTS a flagged customer into being that level. Two hard realities had to be handled: (1) existing customers have real order history (ya_contracts) but a brand-new customer has none — only potential; (2) segment is missing on ~95% of accounts and the only backfill (web-scrape) is keyed by email domain, so personal/webmail customers can't be segmented that way. Ben also corrected that peer/competitor suppliers must NOT be excluded — some sub-hire from us, so they are a legitimate trade segment."
phase: build
version: 9
---

# TS-003: Segmentation+score → account-level: the conversion-layer design (two-track grid + suggest→confirm machine)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
