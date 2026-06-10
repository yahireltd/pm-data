---
id: TS-002
slug: teresa-effectiveness-research-first-readout-of-the-21-month-
title: Teresa effectiveness research — first readout of the 21-month A/B test + model leakage audit
project: yasystem
created: 2026-06-10T01:54:34Z
updated: 2026-06-10T02:00:36Z
decisions:
  - "Teresa's verdict, measured for the first time from its own built-in randomised test (75/25 send/holdout, Aug 2024 – Jun 2026, 12,314 quotes, near-perfect integrity): emailed quotes converted 17.35% vs 16.25% for the silent holdout — +1.1 percentage points, NOT statistically significant. Revenue per allocated quote £68.34 vs £62.93. The separate follow-up-reminder experiment showed no signal. Decision: leave Teresa running (near-zero cost, possibly small positive) but never rely on it; any replacement in the sales redesign must beat this measured baseline; and KEEP the A/B harness in whatever replaces it — the experiment infrastructure was excellent, what failed was that nobody ever read it out."
  - "Model leakage caught and made a standing rule: the quote-conversion model v2's headline AUC 0.98 was 82% data leakage — its top features were deposit fields, and a deposit basically IS a conversion. The honest v3 (deposit features removed, documented in the model's own metadata as \"v3_honest\") scores 0.845. Decision: only v3 may be used or quoted; every future model gets a leakage audit (top features checked for at-prediction-time validity) before its metrics are trusted — data leakage has repeatedly burned our projects."
  - 'Sales-assignment analysis (observational, ~31k quotes since Aug 2024): sales select on value × relationship (93% of £1k+ repeat quotes taken vs 11% of small new ones). The apparent sales effect is LARGEST on new customers (+14 to +26 percentage points by band) and ZERO-to-NEGATIVE on small repeat customers (41.9% assigned vs 43.9% unassigned) — repeat customers broadly convert on their own (43–47% untouched). Implications adopted for the redesign: small-repeat → system-only handling is correct, not a compromise (and frees the 57% of those quotes currently absorbing sales attention); human selling earns its keep on NEW customers ("sell on trust and care", now with numbers); the hardest pocket is big new customers (£1k+ new converts at only 26% even with sales) — where the in-depth/lifetime process redesign matters most. CAVEAT: assigned partly means "customer engaged", so all gaps are upper bounds on the true causal effect.'
actions:
  - text: "Feed the readout into the 12 Jun Sales Coherence workshop (already in the M-005 briefing) and into the stop-chasing v2 design under PP-002: targeting confirmed (Teresa's pool converts ~16–17%, avg £371, vs 45% for sales-assigned quotes), 1-in-6 of the \"junk\" still converts, and the under-£150 band shows the biggest (unproven) lift."
  - text: Rotate the read-only database password that is hardcoded in the ML pipeline script on the exploration branch (secret committed to the repo), and commit the Teresa analysis scripts (currently /tmp/teresa_ab*.py on Austin's Mac) into the repo so the readout is repeatable.
  - text: "To make the assignment effect causal rather than observational, run the Teresa trick on assignment: randomise sales assignment for borderline-band quotes (the A/B allocation infrastructure already exists in the codebase) — propose as part of stop-chasing/routing v2 under PP-002."
side_quests:
  - The old Teresa stats page only measures upsell nudges (discount reminders, slot-widening), not conversion — it could never have answered "does Teresa work". Extend or replace it with the conversion-vs-holdout readout so the loop that was missing for 21 months becomes a screen someone owns.
problem: Teresa (the automated follow-up emails on unassigned quotes) was believed ineffective but had never been measured — and the sales redesign (PP-002, workshop 12 Jun) needs facts, not opinions. Separately, the quote-conversion model's headline accuracy needed validating before anyone builds lead routing on it.
success_criterion: A causal answer on whether Teresa improves conversion (from its own built-in randomised test), a trustworthy model accuracy number (leakage ruled out), and both recorded into the 12 Jun workshop briefing.
version: 4
---

# TS-002: Teresa effectiveness research — first readout of the 21-month A/B test + model leakage audit

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
