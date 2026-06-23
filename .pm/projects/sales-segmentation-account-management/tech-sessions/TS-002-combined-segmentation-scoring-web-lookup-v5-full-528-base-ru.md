---
id: TS-002
slug: combined-segmentation-scoring-web-lookup-v5-full-528-base-ru
title: "Combined segmentation + scoring web-lookup (v5): full 528-base run, method + score-reproducibility analysis"
project: sales-segmentation-account-management
created: 2026-06-23T18:44:06Z
updated: 2026-06-23T18:45:09Z
decisions:
  - "METHOD (v5 — \"one scrape, two outputs\"). A single web-lookup pass per customer emits BOTH the segment AND the score; the scorer is BLIND to spend (the input file carries domain+company only). Per item: WebFetch https://<domain> → try https://www.<domain> → ONE WebSearch fallback if both fail; STEP-0 personal/shared/proxy rule (.ac.uk/.gov.uk/.sch.uk + webmail + proxy domains → classify the NAMED org with label shared-domain, or Personal if it's just a person on a shared host); follow redirects. Fan-out = parallel general-purpose subagents, 12 customers/batch, ~10 concurrent, in waves (44 batches + a 30-row patch to repair index drift). Merge by domain (dedupes overlap); join spend LOCALLY after the blind scrape. Spec: /tmp/seg_spec.md (v5). Output: ~/Documents/segmentation_scored_528.csv — LOCAL ONLY, customer PII, never committed to a repo. OP NOTE: firing all 44 subagents at once tripped a server-side rate-limit; throttle to ~10 concurrent in waves."
  - "FINDINGS (aggregate; row-level in the local PII CSV). Coverage 528/528. Value concentration: Events & Entertainment 53.7% / Media & Marketing 15.7% / Hospitality 6.5% (top-3 ≈ 76%). Tiers: A 212 accts (48% of value) / B 204 (38%) / C 112 (13%). Focus funnel from 528: drop 84 not-events-relevant (9.4% of value) + 34 peer/competitor suppliers → ~410 real prospects → 212 Tier-A (too many for boutique named-account treatment; ~193 in the top-3 industries). Staffable named-account book = high-value × high-potential (311) × not-yet-captured (the DERIVED white-whale). Taxonomy conclusion → ADR-007; scoring reproducibility + variance causes → ADR-008."
actions:
  - text: "NEXT: (1) adopt the v5 run as the scoring baseline + re-score the live customer_sales_scores (fixes ~50+ materially wrong v0 scores — false-A people, false-C venues); (2) productionise the one-pass scrape as a repeatable Yii command + closed-vocab tables + review queue + back-fill the base; (3) extend toward 1000 — export customers #529–1000 by lifetime spend (ya_customers ⋈ ya_contracts.contractTotal), exclude the 528, segment+score the delta, merge; (4) Ben/team to confirm ADR-007 + ADR-008 (both proposed)."
    ticket: T-0474
side_quests: []
problem: |-
  ADR-001..006 defined a segment taxonomy but none was validated against the real customer base, and the 12-Jun scoring (TS-001) was a one-off that was never re-run — so we could not tell whether the scores reproduce, or whether the taxonomy holds at scale. We ran all 528 scored customers through ONE combined web-lookup pass that both segments them (taxonomy v5) and re-scores them (TS-001 rubric, blind to spend), then compared fresh-vs-original scores and mapped value/workload.</problem>
  <parameter name="success_criterion">All 528 customers segmented to taxonomy v5 and re-scored; fresh-vs-original scores compared to quantify reproducibility and diagnose the causes of divergence; value-by-segment and the focus/workload funnel quantified; the method captured so it re-runs and can extend toward 1000. Durable conclusions land in ADR-007 (taxonomy) and ADR-008 (scoring); row-level data stays in the local PII CSV.
phase: build
version: 3
---

# TS-002: Combined segmentation + scoring web-lookup (v5): full 528-base run, method + score-reproducibility analysis

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
