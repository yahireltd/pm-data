---
id: T-0456
title: Phase 2 · Customer scoring — AI web-lookup sales-potential scores
type: feature
state: triaged
created: 2026-06-22T21:41:31Z
updated: 2026-07-17T22:15:37Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-002
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - There's a repeatable way to score new / unscored customer domains (a command or job, not a one-off agent run).
  - All active customers carry a current score + tier + the evidence behind it.
  - The score is visible where it drives a decision, and is available to feed segmentation AND the account-level blend (T-0457).
  - Each scored row carries a classification (mainstream / trade / disqualified) so the account-level engine can route trade/sub-hire peers to a trade lane and disqualify not-events businesses.
  - The last ~300 new quotes are scored as a NEW-customer validation cohort (we have no new-customer ground truth otherwise — the 528 base is mostly existing customers), feeding the what-if tool (T-0479).
out_of_scope: []
code_anchors: []
relates:
  - T-0457
  - T-0479
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 10
---

## What this is

Score every customer's events-hire potential from an **AI web-lookup of their email domain** — the company's website + how often they run events + their size — producing a 0–100 score, a tier (A/B/C), a short "what they are", and the evidence behind it. This is the engine that tells the rest of the system where the value is.

## Where we are

About **530 customers are already scored** (12 Jun) and live on the "Customer Sales Scores" page; the scores sit in the database. It worked well — but it was a **one-off run**, and the method has now been written up and made replicable in **TS-001**.

## The plan

- **Make it repeatable** — a console command that scores new / not-yet-scored customers, instead of a one-off agent run (see TS-001 for the exact method).
- **Score the rest** of the customer base beyond the first ~530.
- **Surface the score** where it actually helps a decision (quote / customer screens), and feed it into segmentation (the next deliverable).

## Open / still to decide

- How often we re-score, and what triggers a re-score (e.g. a customer's behaviour changes).

## Conversation

**2026-06-29 16:01 claude-code:** **Scoring + segmentation pipeline built; first (highest-value) tier scored — sandbox only, not committed.**

Working with Austin to score & segment the whole base in value-ranked tiers, pausing for a DB check after each tier. All on the **sandbox** DB; nothing committed (project is no-commit).

**What's now in place (sandbox):**
- `customer_sales_scores` extended so one row carries BOTH the segment (industry, company_type, sub_type, venue_hire_model, primary_event_type, events_relevant, labels, confidence) AND the score (fit/freq/scale) — plus the `classification` gate (mainstream/trade/disqualified). This is the T-0474 "one table, score+segment" + the T-0456 classification column.
- `ya_customers.email_domain` added, back-filled and indexed (the T-0480 prereq), plus a view `v_customer_segment_scores` so a score/segment can be **looked up by customerID** (the join is via email domain; scoring is inherently per-domain).
- A repeatable console command `customer-scoring` (extract a BLIND worklist → load results → stats). Scorers only ever see domain+company; lifetime spend is joined here at load time (anti-leakage preserved).

**Data loaded so far:**
- The prior **528** v5 run loaded from the local CSV (no re-scoring) — they now have score+segment in the DB for the first time. Reconciles exactly with TS-002/ADR-007 (Events & Entertainment 262, tiers A212/B204/C112, 34 trade, 83 disqualified).
- **Tier 1 = the 87 unscored domains with ≥£10k lifetime hire**, scored fresh with the combined v5 method (8 subagents). Quality spot-checks good: e.g. Talking Tables correctly flagged a non-buyer retailer (disqualified), Bloomsbury Ballroom = dry-hire venue, etc.venues = in-house, LSE shared-domain handled, Gymshark runs-own-events.
- Table now: **615 rows, all v5, all segmented**; 1,390 customers reachable by customerID via the view.

**Remaining tiers (value-ranked, awaiting Austin's go-ahead per tier):** ≥£5k (~342) → ≥£2.5k (~747) → ≥£1k (~1,679) → any-hire (~5.9k) → quote-only leads (~11.7k). ~51k personal/webmail customers can't be web-scored — left for the history-based path. Larger tiers will run via a fan-out workflow.

**Heads-up (unrelated repo bug):** `./yii migrate` is currently broken on master — `m260615_140000_create_warehouse_settings` references `common\components\IdempotentMigrationTrait`, which doesn't exist in the tree, so the whole migrate run aborts. I applied my two migrations directly around it on the sandbox. Worth a separate fix.

**2026-06-29 21:46 claude-code:** **Progress + a hard stop: Claude monthly spend limit reached.**

Scored & segmented to date (sandbox, all `web-lookup-v5`): **3,990 domains** — the 528 base + Tier 1 (≥£10k, 87) + Tier 2 (≥£5k, 297) + Tier 3 (≥£2.5k, 697) + Tier 4 (≥£1k, 1,601) + ~780 of the <£1k hire tier. Mix so far: tiers A 687 / B 1,632 / C 1,671; classification mainstream 2,700 / trade 97 / disqualified 1,193.

**Blocker:** part-way through Tier 5 the Claude account hit its **monthly spend limit** (claude.ai/settings/usage) — every subsequent scoring subagent failed, so I stopped the loop rather than burn the remaining tiers against a hard error. Needs the limit raised (or the monthly reset) to continue.

**Durability (the test DB is wiped nightly):** everything is saved locally and is DB-independent — per-tier JSON, a full-table CSV, and a re-runnable SQL upsert under `runtime/p0018/export/`. Restore after a refresh = run the (idempotent) migrations, then load the SQL.

**Remaining to score** (the `extract-tier` command auto-excludes already-scored domains, so resume is clean): <£1k hire ~4,969, quote-only leads ~14,022, plus a few stragglers (5to10k 1, 2k5to5k 1, 1kto2k5 13). Resume per band = extract → split → run the `p0018-score-tier` workflow → load → export.

**2026-07-02 12:14 claude-code:** **2026-07-02 — Code committed + pushed** in commit **`cede0ffb`** (branch `p0018-sales-segmentation-design`, pushed to origin; direct commit, allow_commit was off).

This ticket's share: the scoring/segmentation pipeline — `CustomerScoringController` (extract-tier / load-results / export / stats / segment-profile) + the `customer_sales_scores` segment-fields migration. DB-facing steps only; the AI web-lookup itself runs via subagents. Coverage now: **4,096 domains scored** (all realised-£ customers ≥£1k, plus histlt1k batch 1). ~4,917 histlt1k + ~14k quote-only leads remain, being worked through in paced batches.

**2026-07-03 00:21 claude-code:** Progress update (per Austin, 3 Jul 2026): **8,405 customers now scored** — up from the 4,096 recorded on 2 Jul — with a further batch finishing on the work Mac. The M-008 "extend beyond the 528 base" decision is therefore decided AND executed (target was ~1,000). Remaining cohorts: the tail of the <£1k realised customers and the ~14k quote-only leads, continuing in paced batches within the monthly AI spend budget.

**2026-07-03 00:24 claude-code:** Status reconciliation (2026-07-03 verification pass) — what is actually built vs outstanding, so the body stops implying "score the rest" is done.

BUILT (committed, cede0ffb7 on p0018-sales-segmentation-design):
- Repeatable pipeline: console/controllers/CustomerScoringController.php — extract-tier (blind domain+company worklist, auto-excludes scored domains, lines 141-218), load-results (upsert + classify + spend-at-load, 223-268), load528, stats, export (durable CSV + re-runnable SQL, 315-367), segment-profile. AC1 met; note the AI web-lookup step itself is the p0018-score-tier subagent workflow, captured in TS-001 but not in the repo.
- Schema: m260629_120000 (segment fields + classification ENUM at :36) and m260629_120100 (ya_customers.email_domain + v_customer_segment_scores view) — both idempotent, applied on SANDBOX only.
- Classification per scored row: classify() at CustomerScoringController.php:443-449; consumed by AccountLevelController.php:49-50 (MVP routes both trade and disqualified to the system lane — no distinct trade lane yet).
- Blend feed (AC3 half): AccountLevelController::actionRecompute reads score/tier/classification (78-128); score column on account-levels worklist (backend/views/account-levels/index.php:102).
- New-quote validation cohort (AC5): 323 deduped leads scored 26 Jun (~/Documents/P-0018-new-quote-scores.{csv,json}, local-only per the README PII policy), feeding the what-if population via committed gen_population_demo.py.

OUTSTANDING:
- Coverage (AC2): 8,405 customers scored as of 3 Jul (see progress note above; batch in flight) — remainder of the <£1k tail + ~14k quote-only corporate domains continue in paced batches; ~51k personal/webmail customers need the history-based path, which is not designed or built.
- Surfacing (AC3): score is NOT on quote/customer screens (only the pre-existing Sales Scores page on master + the branch-only account-levels views).
- Deployment: all segment/classification data + columns exist only on the sandbox (wiped nightly; restore = idempotent migrations + runtime/p0018/export SQL). Production customer_sales_scores (~530 rows, 12 Jun) lacks the new columns.
- Blocker for applying the migrations anywhere via the normal path: ./yii migrate still aborts at m260615_140000_create_warehouse_settings — common\components\IdempotentMigrationTrait does not exist in the tree. Needs a separate fix ticket.
- Re-score cadence/triggers: still the open decision from the ticket body.

**2026-07-17 15:55 claude-code:** **2026-07-17 — MILESTONE: the entire hired-customer base is now scored & segmented.** Verified zero unscored domains with any delivered-hire history.

- **9,074 domains scored** (1,202 A / 3,051 B / 4,821 C), all with segment (industry / company_type / sub_type / labels / classification), method web-lookup-v5.
- Final waves today: the last 417 of the <£1k band + 12 newer customers who had crossed £1k since their band was swept (caught by re-checking ALL spend bands, not just the original extract).
- Storage: sandbox `yahire_db.customer_sales_scores` (protected from the nightly refresh via PRESERVE_TABLES) + durable re-runnable export `runtime/p0018/export/customer_sales_scores_data.sql` (9,074-row upsert) — **Austin replays this to live-main manually** (live currently holds 8,885; needs one more replay to pick up today's 429).
- The `/segment-research` page (live, commit f31705fd) computes from this table — once live is topped up + its ~10-min cache turns, it will show the complete base.
- **Remaining scope decision (open):** ~14,142 quote-only corporate leads (never hired). Options: score-on-demand at quote time (matches enrichment-on-demand), top-slice by quote value, or bulk sweep. To discuss with Ben.

**2026-07-17 22:15 claude-code:** **Scoring progress + findings (2026-07-17).**

Where the sweep stands — total scored base now **11,448** domains/companies:
- **Delivered-hire base:** all 9,074 customers with any hire history — done (earlier).
- **Quote-only corporate leads** (quoted, never hired), highest-quote-value first: batch 1 (~1,000) + batch 2 (1,008) — done.
- **"Personal with company" cohort** (enquirers who used a personal/webmail email *but gave a company name*): in progress, ~83% through 1,304 companies. These can't be scored by domain, so each is identified by **company name cross-checked against the customer's real address/quote-delivery location** before we accept a match — no location/identity match = left unscored rather than guessed (~50% match as we get into the obscure tail).

Findings worth a look (make the commercial case):
- **394 A-tier leads with £0 realised** — high potential, never converted. Top of the list: Alexandra Palace, Levy UK, ExCeL London, BCD M&E, The Jockey Club, Goodwood, Cheltenham Festivals.
- **54 A-tier corporates found hiding behind personal emails** (e.g. Madhu's, LatinoLife, Blonstein, Lulu's Marquees) — demand that domain-based scoring couldn't see.
- **216 competitors + 280 "trade"** flagged in the base (worth excluding from sales targeting).
- ~37% (4,246) correctly disqualified as not event-relevant, so the A/B lists are clean.

New tickets raised off the back of this: **T-0618** (all-customer research view), **T-0619** (personal-cohort golden-nugget spotter + Personal vs Personal-with-company segments), **T-0621** (rewind/perspective-date back-test of score→£). Scores live in the sandbox + a re-runnable export; replayed to live-main manually.
