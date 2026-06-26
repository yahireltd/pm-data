---
id: T-0481
title: Quote-intrinsic potential model — white-whale detection at first quote (items + location + value)
type: feature
state: triaged
created: 2026-06-26T16:33:34Z
updated: 2026-06-26T16:33:34Z
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
version: 1
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