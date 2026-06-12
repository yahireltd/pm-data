---
id: T-0360
title: Check out a sales scoring cost on credits
type: feature
state: review
priority: p2
created: 2026-06-11T23:57:22Z
updated: 2026-06-12T01:47:12Z
project: yasystem
section: null
parent: null
children: []
order: 6144
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - a cost in api credits for a full scrape of excel.london example
  - looking up socials should reveal event frequency - needs to be an event not just a post
  - we are trying to weigh up the cost of scoring all customers / new screened / new all / existing + new all so get an idea of monthly new customers (filter by domain)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260612-0008
    model: claude
    started: 2026-06-12T00:08:50Z
    status: completed
    summary: |-
      **What we did:** Worked out what it would cost in AI credits to automatically "score" each customer for sales potential, by actually running the lookup live on the example customer (excel.london). The lookup splits personal vs business email addresses for free, then reads the business's website to identify its industry, then — only for promising industries like venues and caterers — does a deeper sweep of its event listings and public financial records. A full deep lookup costs roughly 10p–20p per customer depending on the AI model used; the cheap industry check costs under a penny. We also found that Instagram/Facebook can't be read automatically, but event-listing sites and the company's own what's-on page give better evidence of real events anyway, and UK financial records are free via Companies House. Full cost tables and monthly scenarios are in the ticket comment.

      **Why:** Before building customer scoring, we needed to know whether running it across all customers (or just new ones) is affordable, and whether the lookup can actually answer "how often does this business run events?"

      **If we did nothing:** We'd be guessing at the budget — either over-committing to scoring every customer at an unknown cost, or dismissing the idea as too expensive when scoring all new corporate customers actually costs single-digit pounds per month.

      **The benefit:** A clear, evidence-based price list: scoring each month's new corporate customers ≈ £6–£14/month; a one-off backfill of all existing corporate customers is the only material spend and a two-stage screen keeps it to low hundreds of pounds. The team can now decide scope (new only vs backfill) with real numbers.
    ended: 2026-06-12T01:47:12Z
    test_plan: |-
      1. Open T-0360 and read the findings comment (dated 2026-06-12) — check the pipeline stages and cost table read sensibly to you.
      2. Sanity-check the headline numbers: full scrape ≈ $0.10 on Haiku / $0.23 on Sonnet per customer, built from ~50k input + ~3k output tokens + 3 web searches at $10/1,000 (pricing pulled from platform.claude.com on 2026-06-12).
      3. Spot-check the worked example: excel.london classified as a large events venue from one homepage fetch; event frequency evidenced by dated events (its what's-on page, Ents24/tradefest aggregators, "400 events/year") — not social posts.
      4. Note the two cautions: Instagram/Facebook block scraping (use event aggregators instead), and Companies House name lookups can match the wrong similarly-named entity — domain→legal-entity resolution must come from the website itself.
      5. Decide the open follow-up: the monthly volume scenarios use example counts (500 new/mo, 40% corporate). One DB query on the test box (distinct customers per month, personal-domain exclusion on email domain) turns them into firm figures — say the word and it becomes a ticket.
      6. Housekeeping check: the ticket's pinned branch (refund-hardening-t0331-stripe-dryrun, a mis-pin from last night) is now cleared — the ticket falls back to the project default.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-12T01:47:12Z
version: 11
---

So the idea to score a customer is to first of all use current personal /  coroprate split -this is done using the email domain so non custom domains (gmail/hotmail/outlook.com) are personal customers else corporate. Then we need to identify industry. Look u p the domains website. So for example the customer domain is  customer@excel.london we would send an agent to look at their website. and if found to be a venue / caterer etc then look up their socials and public financial records to asses their potential value, event frequency etc. 

we want to figure out how much this lookup would cost in credits per customer

## Conversation

**2026-06-12 01:46 claude-code:** **Findings: what a per-customer lookup costs in API credits (worked example: excel.london, run live 2026-06-12).**

**The pipeline and what each stage costs**

- **Stage 0 — personal/corporate split** (gmail/hotmail/outlook/yahoo = personal, else corporate): plain code, no AI, **free**. This filters out the majority of customers before any credits are spent.
- **Stage 1 — cheap industry classify**: fetch the domain's homepage and ask a small model "what business is this?". One page fetch (free, ~3,000 tokens of content) + one Haiku call. **~0.5–0.7¢ per customer** ($0.005–0.007), and roughly **0.3¢** if run through the Batch API (50% off, fine for overnight scoring). The excel.london homepage alone was enough to classify it as a large events venue.
- **Stage 2 — full enrich** (only for customers Stage 1 flags as venue/caterer/events/etc.): an agent run doing ~3 web searches + ~5 page fetches covering the website, event listings, and public financials, then writing a score. Measured shape: ~50k input tokens + ~3k output tokens + 3 searches.

**Cost per FULL scrape (the excel.london number, AC 1):**

| Model | Per full scrape | Per 1,000 customers |
|---|---|---|
| Haiku 4.5 | **~$0.10 (~8p)** | ~$100 |
| Sonnet 4.6 | **~$0.23 (~18p)** | ~$230 |
| Opus 4.8 | ~$0.36 (~28p) | ~$360 |
| Fable 5 | ~$0.68 (~54p) | ~$680 |

(Web searches are $10 per 1,000; page fetches are free apart from their token content; prompt caching assumed — without it roughly double the token component. Batch API halves the token component again for non-urgent runs.)

**Event frequency from socials (AC 2) — important practical finding:** Instagram/Facebook block automated scraping, so "look up their socials" doesn't work literally. What DOES work, and is better evidence anyway: the company's own what's-on/events page plus public event aggregators (Ents24, tradefest.io, allevents.in) and press. For excel.london this surfaced dated, named events ("up to 400 events/year", 10 international congresses booked for 2026, Formula E in July) — actual events, not posts, which is exactly what the criterion asks for. This fits inside the 2–3 searches already costed above.

**Financial records:** UK companies are free via the Companies House API (no credits, generous rate limits). One caution from the live run: searching "ExCeL London" returned several unrelated similarly-named companies — the agent needs to resolve domain → legal entity carefully (registered name from the website footer/T&Cs) or values will come from the wrong company.

**Monthly scoring scenarios (AC 3)** — per-customer × volume; using the two-stage funnel (Stage 1 on everyone corporate, Stage 2 only on relevant industries, assume ~⅓ escalate):

| Scope | Formula | Example: 500 new/mo, ~40% corporate | Example cost (Haiku/Sonnet for Stage 2) |
|---|---|---|---|
| New, screened (corporate only) | N×0.4×Stage1 + N×0.4×⅓×Stage2 | 200 classified, ~67 enriched | **~$8 / ~$17 per month** |
| New, all (incl. personal check) | N×Stage0 free + as above | same | same |
| Backfill all existing corporate | M×Stage1 (batch) + M×⅓×Stage2 | e.g. 20,000 corporate | ~$60 classify + ~$670–$1,500 enrich, one-off |
| Everything monthly (existing+new re-score) | impractical — re-score on trigger (new booking) instead | — | — |

Exact customer counts need one query against the live DB (distinct customers in the last 12 months, grouped by month, with the personal-domain exclusion list applied to the email domain) — that turns the formulas above into firm £ figures. Happy to run it on the test box as a follow-up.

**Recommendation:** two-stage funnel, Haiku for Stage 1 (batched), Sonnet for Stage 2 enrich. At those rates, scoring every new corporate customer costs single-digit pounds per month; the only material spend is the one-off backfill, which the Stage 1 screen keeps under control.

(Also tidied: this ticket had branch `refund-hardening-t0331-stripe-dryrun` pinned — a slip from last night's session, nothing here touches that branch — cleared it.)

**2026-06-12 01:47 claude:** Run run-20260612-0008 completed — **What we did:** Worked out what it would cost in AI credits to automatically "score" each customer for sales potential, by actually running the lookup live on the example customer (excel.london). The lookup splits personal vs business email addresses for free, then reads the business's website to identify its industry, then — only for promising industries like venues and caterers — does a deeper sweep of its event listings and public financial records. A full deep lookup costs roughly 10p–20p per customer depending on the AI model used; the cheap industry check costs under a penny. We also found that Instagram/Facebook can't be read automatically, but event-listing sites and the company's own what's-on page give better evidence of real events anyway, and UK financial records are free via Companies House. Full cost tables and monthly scenarios are in the ticket comment.

**Why:** Before building customer scoring, we needed to know whether running it across all customers (or just new ones) is affordable, and whether the lookup can actually answer "how often does this business run events?"

**If we did nothing:** We'd be guessing at the budget — either over-committing to scoring every customer at an unknown cost, or dismissing the idea as too expensive when scoring all new corporate customers actually costs single-digit pounds per month.

**The benefit:** A clear, evidence-based price list: scoring each month's new corporate customers ≈ £6–£14/month; a one-off backfill of all existing corporate customers is the only material spend and a two-stage screen keeps it to low hundreds of pounds. The team can now decide scope (new only vs backfill) with real numbers.
