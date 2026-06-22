---
id: TS-001
slug: customer-sales-scoring-the-replicable-ai-web-lookup-method-r
title: Customer Sales Scoring — the replicable AI web-lookup method (runbook)
project: sales-segmentation-account-management
created: 2026-06-22T19:38:34Z
updated: 2026-06-22T19:40:04Z
decisions:
  - |-
    THE REPLICABLE AGENT PROMPT — run ONE subagent per batch of ~5 domains, in parallel; the scorer must stay BLIND to the customer's spend (anti-leakage). Verbatim (substitute the 5 domains in the list):

    «You are scoring potential B2B customers of a London furniture/catering-equipment hire company (they hire chairs, tables, crockery etc. for events). For EACH of these 5 domains: <domain1 … domain5>

    Procedure per domain: WebFetch https://<domain> (try https://www.<domain> if that fails) and classify the business. If it is clearly events-relevant (venue, caterer, event producer, exhibitions, hospitality, brand activations), you may run AT MOST ONE WebSearch to establish event frequency (look for dated events, not social posts). If the homepage shows it's clearly NOT events-relevant, stop there (cheap exit, no search). If both fetch attempts fail, use one WebSearch instead.

    Score each on this rubric:
    - fit (0-40): how relevant the industry is to event furniture/equipment hire (event producers/venues/caterers ~35-40, hospitality-adjacent ~20-30, generic corporate that occasionally runs events ~5-15, irrelevant 0-5)
    - freq (0-30): evidence of how often they run events (dated/regular events = high; none found = 0)
    - scale (0-30): company size/financial standing — but cap at 10 if fit < 15 (a giant irrelevant company is still low value)
    - score = fit+freq+scale; tier: A if >=70, B if 40-69, C if <40

    Your final message: ONLY a JSON array, one object per domain: {"domain":…, "business":"<8 words max>", "fit":n, "freq":n, "scale":n, "score":n, "tier":"A|B|C", "evidence":"<one short sentence>", "searches_used":n}. No other text.»
  - "END-TO-END PIPELINE. (1) Pull the customer email domains, ranked by lifetime spend (most valuable scored first). (2) Fan out parallel subagents, ~5 domains per batch — the first run was ~56 batches (≈280+ domains; ~530 rows incl. a sample + the top-by-spend). (3) Each agent returns ONLY its JSON array. (4) Merge all the JSON and join spend back in with a small python script → build customer_sales_scores.sql → copy to the server → import. Table: customer_sales_scores(domain PK, company, score, tier, business, evidence, lifetime_spend_at_scoring, scored_at, method DEFAULT 'web-lookup-v0'). Cost discipline: homepage WebFetch first, cheap-exit if not events-relevant, at most ONE WebSearch. Tiers A≥70 / B40-69 / C<40. The Events/Yr + Next Event columns come from the same web-lookup (the freq/evidence), surfaced via a sibling table customer_upcoming_events."
  - "WHERE IT LIVES / RECOVERY (it was nearly lost as undocumented one-off work, 22 Jun). Three full copies survive: (a) the live DB — customer_sales_scores + customer_upcoming_events, each row stamped with method='web-lookup-v0'; (b) the file ~/Desktop/customer_sales_scores.sql (~530 rows — currently the ONLY local copy); (c) the original session transcript (pm-tool project, session ffe4c329). Shipped UI: SalesScoresController + sales-scores/index.php; migrations m260612_031500_create_customer_sales_scores_table + m260612_033000_create_customer_upcoming_events_table (on Yasystem master, T-0360 scoring / T-0363 UI)."
actions:
  - text: "Productionise the scoring: turn this one-off agent run into a repeatable Yii console command (e.g. ./yii customer-scoring/score --only-unscored) so re-scoring new/unscored customers is a version-controlled command, not a one-off. The command pulls unscored domains, runs the batch-of-5 agent prompt (above), and upserts into customer_sales_scores."
  - text: Back up ~/Desktop/customer_sales_scores.sql (the only local copy of the 530 scored rows) somewhere durable — commit it into the Yasystem repo (or attach it here) so it isn't a loose file that a Desktop clear-out would lose.
side_quests: []
problem: |-
  On 12 Jun an agent scored each customer's email domain for events-hire potential by AI web-lookup (the "Customer Sales Scores" page). It was a one-off agent run and the method was never written down — so we couldn't re-run it for new customers or hand it to another agent. This captures the exact, replicable method so any agent (or person) can reproduce it.</problem>
  <parameter name="success_criterion">An agent can take a fresh list of customer email domains and reproduce the same shape of result (business description, fit/freq/scale, score, tier, evidence) without needing the original session — and ideally via a repeatable command rather than a one-off run.
phase: build
version: 6
---

# TS-001: Customer Sales Scoring — the replicable AI web-lookup method (runbook)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
