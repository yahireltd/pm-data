---
id: T-0481
title: Quote-intrinsic potential model — white-whale detection at first quote (items + location + value)
type: feature
state: triaged
created: 2026-06-26T16:33:34Z
updated: 2026-06-30T12:46:27Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-002
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Ben
assignee: null
acceptance_criteria:
  - A quote-intrinsic POTENTIAL signal predicts a customer's annualised £ potential from the FIRST quote alone (line items via quoteitems×ya_products, location via quoteaddr→postcodescategory/venues, quote money + seasonality) — no order history, no web score required — and blends in as a THIRD potential source with its own tunable weight (down-weighted as realised history accrues).
  - Ships first as an INTERPRETABLE scorecard (sales-readable, no trained model); the ML upgrade swaps in behind an identical (£, confidence) contract only once it beats the scorecard on a time-split holdout.
  - "Strictly leakage-safe: features computed only from data timestamped <= t0 (creation-version reconstructed from QuotesArchive/QuoteitemsArchive; fallback quotes.edited=0); label = became a high-realised/LTV customer within N months, resolved by the normalised-email graph (not customerID) with a 30-day cooling-off; never conditions on this quote's conversion/contract value."
  - "SEGMENT is a feature: the industry/company_type from ADR-007, using ONLY the segment derivable at first-quote time (domain web-lookup at t0; missing for webmail leads — the model must handle missing segment), plus segment×item-mix interactions. Segment is partly encoded by items+location already, so it adds signal without being required."
  - "Guardrails as CODE: the signal feeds expected_value (steering/queue) ONLY and can NEVER promote a level (levels stay banded on the realised £ floor); a quoteTotal-ablation + precision@k on SMALL first quotes are ship gates before its weight rises above advisory."
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
---

## What this is

Ben's idea (2026-06-26 phone): detect potential white whales from the **first quote alone** — the **line items** (scale/premium-ness), the **location** (postcode/venue tier), the quote value + seasonality — so a big new customer is visible *before* any history or web score. Blends into the account-level model as a **third potential source with its own tunable weight** (T-0479).

Full design: `~/Documents/P-0018-quote-intrinsic-model.md` (BUILD verdict, 3 designs 7.5–8/10). Decisions: **TS-003**.

## Hard dependency
**Sequenced behind [[T-0457]]** — `customer_account_levels` + the realised-£ floor blend don't exist yet (only `customer_sales_scores` does), so this model's integration/storage/[[T-0479]] slider have no home until T-0457 is built or stubbed.

## Why it matters (evidenced)
On the 137-domain new-quote sample: 24% A-tier white whales arrive in the new-quote stream looking like a small first quote. This is the signal that catches them early. Validated against the demo (selainternational £24k first booking = a real big new lead).

## Build = scorecard now, ML later
See the design doc §3 (leakage-safe label), §4 (model/calibration), §5 (blend integration), §6 (scorecard MVP), §10 (edge cases). Real tables verified in recon: `quotes`, `quoteitems`, `ya_products`, `quoteaddr`, `postcodescategory`, `venues`, `ya_contracts`, `QuotesArchive`/`QuoteitemsArchive`/`QuoteChangesLog`.

## Conversation

**2026-06-26 21:21 claude-code:** **Data-leakage audit (26 Jun) — important, read before training.** Ran an adversarial audit of the training extract (`QuotePotentialController`). Full write-up: `docs/p0018-sales-segmentation/P-0018-leakage-audit.md`. Fixes committed on branch `p0018-sales-segmentation-design` (commit 635db3fc5).

**What was wrong (the two classic killers, both real):**
- **The label leaked the quote's own conversion.** Contracts were matched to a lead by email domain only, so a quote's *own* slow conversion counted as its "future value" — the model was quietly learning "will this quote convert" instead of "will this customer become valuable." **Fixed** (exclude the quote's own contract).
- **The extract didn't even run** — it selected a `company_type` column that doesn't exist on `customer_sales_scores`, so it threw and returned zero rows. So **nobody had trained on real data yet** — we caught this *before* any training. **Fixed** (that join was a leak anyway — a 2026 web-score applied to 2022 quotes — and is removed).
- Plus: webmail addresses treated as one customer; duplicate-row fan-outs; and a label that changed every time you rebuilt it. **All fixed.**

**Clean:** no quote status/won/converted field is used as a feature, and contracts never feed the features (label only).

**Still blocked before a real training run:** (1) point-in-time feature reconstruction from the version-1 archives (needs DB); (2) proper customer-identity resolution so a customer using two emails isn't split — prereq **T-0480**.

**REQUIREMENT raised by Austin — the customer must be scored + segmented, and the model must NOT be blind to it.** Removing the score/segment join fixed a leak but mustn't drop the signal. Resolution: **live** score+segment at serve time (no leakage — it's the present), and a **point-in-time** snapshot (`scored_at <= t0`) for training. This makes the model depend on **T-0456** (scoring) + **T-0473/T-0474** (segment vocab+import) landing with a `scored_at` date. Until then `company_type` is NULL and the model leans on quote-intrinsic features only.

**Defences added:** 4 leakage gates now run on every training (`train.py`) and block the ship-gate if tripped — label-shuffle null test, mutable-basket ablation, strict time-split assertion, and a single-feature probe that would catch any reintroduced score/spend column.

**2026-06-26 21:40 claude-code:** **Real-data validation (26 Jun) — the model is NOT viable; ship the scorecard.** Ran the leakage-fixed export against the **sandbox RDS** (read-only): 6,898 first-quote domains over 2 years. The good news: the export runs clean on the real schema, and the leakage gates pass in every configuration (shuffle ≈ base rate, strict time-split, no single-feature leak). The sobering news:

- Genuine new-customer **whales are vanishingly rare** — only **19–33** first quotes (out of 6,898) led to an £8k+/yr customer, depending on how generously we define the target (repeat-only / total / include-the-first-job). The model needs ~150 to train, so it can't.
- More fundamentally: even ignoring the count, the **first-quote features don't beat the quote's own value** — without `quote_total`, the model ranks future value *worse* than just sorting by quote size (ablation gate fails). So "spot a hidden whale from the item mix / location of a small quote" **isn't supported by the data**. The great result we saw earlier was on synthetic data — it was the leakage, not real signal.

**Decision:** keep the **interpretable scorecard** live; **park the ML model**. Revisit only with materially more history, or a deliberately different/lower target — and any such model would still have to clear the ablation gate (beat ranking-by-quote-size) to be worth deploying. Full numbers + the label distribution are in `docs/p0018-sales-segmentation/P-0018-leakage-audit.md` (commit 397b09e8f). Added `--totalValue`/`--coolingDays` export knobs for target experimentation.

**2026-06-30 12:46 claude-code:** **A working, interpretable potential signal — `AOV × events/year` — built and validated. This is the quote-intrinsic potential the ML couldn't deliver, done as a scorecard.**

Two things established on real data (sandbox, branch `p0018-sales-segmentation-design`, uncommitted):

1. **The first quote tracks the customer's average order value** — Spearman 0.66, median ratio quote/AOV 0.93, 75% within 0.5–2×. So the first quote is a sound AOV proxy at enquiry time.
2. **Repeat is a SEGMENT signal, not a quote-value one.** Per-company_type cadence varies hugely (cultural venue ~6 events/yr, event production ~2.2, caterer ~1.2, corporate ~1.0). Score/tier adds only a weak gradient; company_type is the driver. The web-lookup's own "repeat" label is 92% accurate when present.

**Combined estimate `potential = quote × segment events/year` beats quote-value-alone for LIFETIME value: AUC(≥£8k) 0.72 → 0.76, Spearman 0.26 → 0.28.** (Note: against the 12-month repeat label everything scored ~0 — the window is too short for repeat cadence; lifetime is the correct target.) Modest but real and fully interpretable — no ML, clears the spirit of the ablation gate by adding the repeat dimension quote size can't see.

**Built:** `segment_profile` table + `customer-scoring/segment-profile` (computes repeat_rate / events_per_year / AOV per company_type — the `repeat_likelihood` the account model/T-0457/T-0479 want); `common/components/PotentialEstimator.php` (`AOV × events/yr`, small-n guard so a 1-customer segment gets no repeat boost).

**Caveats:** company_type has vocab drift (62 types incl. near-dupes + tiny segments — wants the T-0474 closed-vocab/review-queue to consolidate); the scored set skews to established customers, so absolute rates firm up at full coverage. **Recommendation:** use this as the third potential source (alongside the web-score and quote value), and surface it on the fast-track lane / a repeat flag.
