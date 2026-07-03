---
id: M-008
slug: segmentation-research
title: Segmentation Research
state: held
created: 2026-06-23T12:35:32Z
updated: 2026-07-03T00:21:53Z
scheduled_at: 2026-06-23T14:35:00Z
duration_minutes: 30
location: TBD
project: sales-segmentation-account-management
pre_project: null
milestone: MS-002
organizer:
  kind: human
  name: unknown
stakeholders:
  - name: Ben
    role: Business SME & Systems Architect
    channel: email
    contact: ben@yahire.com
    internal: true
    notify_on:
      - state_change
      - meeting_held
      - outcome_recorded
      - comment_added
      - assigned
      - meeting_scheduled
  - name: Austin
    role: Developer & System SME
    channel: email
    contact: austin@yahire.com
    internal: true
    notify_on:
      - state_change
      - comment_added
      - assigned
      - meeting_scheduled
      - meeting_held
      - outcome_recorded
agenda:
  - topic: Research into customer segments -
  - topic: look into auto industry typing and subtyping
outcomes:
  - description: Decision 1 — Taxonomy v5 (ADR-007) CONFIRMED as the segmentation model. Ben agreed verbally; recorded retroactively 2026-07-03 during the decision-ledger reconciliation. ADR-007 moved to accepted.
    recorded_at: 2026-07-03T00:21:31Z
    owner:
      kind: human
      name: Ben
  - description: "Decision 2 — Scoring v5 ADOPTED as baseline and the live re-score authorised (ADR-008 → accepted). CAVEAT recorded: how stewardship LEVEL is assigned and how POTENTIAL is calculated may still change — Ben is unsure whether to steer by unrealised potential or realised value, and wants a sales-expert opinion. Captured as a new open decision record (steering-basis) rather than blocking the scoring baseline. Recorded retroactively 2026-07-03."
    recorded_at: 2026-07-03T00:21:53Z
    owner:
      kind: human
      name: Ben
attachments:
  - key: meetings/M-008/1783015770072-Screenshot_2026-06-17_at_16-00-28_Ticket_run_planner_idea_Yahire_Zendesk.png
    filename: Screenshot 2026-06-17 at 16-00-28 Ticket run planner idea – Yahire – Zendesk.png
    content_type: image/png
    size: 65804
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-07-02T18:09:31Z
calendar:
  graph_event_id: null
  ics_url: null
kind: scoping
version: 10
---

# **Segmentation Research — findings brief (M-008)**

**For:** Ben · **From:** Austin (with Claude) · **Date:** 2026-06-23 **Project:** Sales Segmentation / Account Management (P-0018) · **Milestone:** MS-002

## **TL;DR**

* We ran **all 528 scored customers** through one web-lookup pass that **both segments and re-scores** each one. Coverage was total — **528/528 classified**.
* We have a **validated segmentation model** (taxonomy v5) and a clear map of **where the value and the work are**.
* The value is **highly concentrated**: the top 3 industries hold **\~76%** of lifetime spend.
* We **replicated the original scoring** for the first time. The scores broadly hold (no systematic bias); where they differ, the new method is mostly **fixing errors in the original run**.
* A few **decisions are needed from you** (below) to lock the model and move to build.

***

## **1. What we did**

Took the 528 customers we scored in June, and for each one looked up its website and produced — in a single pass, blind to how much they actually spend — both:

* a **segment** (what kind of customer they are), and
* a **fresh sales score** (using the exact rubric from the June scoring).

Then we compared the fresh scores to the originals, and mapped lifetime value and workload across the segments. Total lifetime spend in the base ≈ **£19.2M**.

## **2. The model we're proposing (taxonomy v5)**

A customer's "DNA" across nested levels, not one flat list:

* **Industry** (the real sector — ~20 of them) → **Company type** → **Sub-type**
* plus **event-types served** and operational **labels**

Key refinements this round proved out:

* **Industry is separate from company type** (your point exactly — a *production company* can be a low-value film one or a high-value events one; the real sector tells them apart).
* New **"Place of worship / faith organisation"** type — \~£0.3M of churches/synagogues/mosques that had no home before.
* **"peer/competitor" flag** — 34 accounts that are themselves hire suppliers, not buyers (exclude from prospecting).
* **Venue hire model** — `dry-hire` (clients must bring/hire their own = **highest value to us**) vs `in-house` (they cater themselves).
* **"High-potential"** flag for genuinely big/prestige/event-active accounts (the "white whale" becomes a *derived* number — potential minus what we actually capture).

**Coverage:** 528/528 classified, only 1 fell to "Other". The model holds at full scale.

## **3. Where the value is (the focus)**

| Industry               | % of lifetime value | accounts |
| :--------------------- | ------------------: | -------: |
| Events & Entertainment |           **53.7%** |      262 |
| Media & Marketing      |               15.7% |       75 |
| Hospitality            |                6.5% |       32 |
| **Top 3 combined**     |           **\~76%** |      369 |

## **4. How much work that focus is (the workload)**

Starting from 528:

* **Drop 84 not-events-relevant** (9.4% of value — Tesla, Nike, banks, councils): not furniture prospects.
* **Drop 34 peer/competitor** suppliers.
* → **\~410 real prospects.**
* Of those: **Tier A = 212 accounts (48% of value)**, Tier B = 204 (38%), Tier C = 112 (13%).

**The honest message:** 212 Tier-A accounts is too many for boutique named-account treatment. A staffable named-account book = **high value × high-potential (311 flagged) × not-yet-captured** — that intersection is the list worth giving a human.

## **5. Does the scoring hold up?**

We'd never re-run the original scoring before. Now we have:

* **67% of customers kept the same A/B/C tier** (1 in 3 moved) — but **164 of 176 changes are just one tier**, and there's **no systematic bias** (fresh average within 1.4 points of original).
* The differences are **mostly the new method fixing the old one**: the June run scored some *people* as top accounts (no rule for personal/shared email domains) and buried some *real event venues* at near-zero (it gave up when a website didn't load). The new run fixes both.
* Scores are inherently **web-lookup dependent** — the harder a site is to read, the more its score wobbles, and businesses genuinely change over six months.

**Recommendation:** adopt this fresh run as the new baseline, rank accounts by *score* (not hard A/B/C cutoffs) so borderline accounts don't churn, and **re-score on a cadence**. The rubric itself is sound — no change to the maths.

## **6. What's recorded (so you can dig in)**

On the project in pm-tool:

* **ADR-007** — the taxonomy v5 (validated)
* **ADR-008** — scoring v5 baseline + why scores varied + the fixes
* **ADR-009** — reconciliation (what's canon, what's retired)
* **TS-002** — the method + full results
* Earlier strawman decisions (ADR-002/004/005) have been **rejected/retired**.
* Every customer's segment + fresh score + original score sits in a local spreadsheet (kept off the servers — it's customer data).

## **7. Decisions we need from you**

1. **Confirm taxonomy v5** (ADR-007) as the segmentation model — you own this call.
2. **Adopt the fresh v5 scoring as the baseline** and authorise re-scoring the live customer scores (ADR-008). Expect \~⅓ of tiers to move — that's the corrections landing.
3. **Extend to the full customer base (\~1000):** the 528 are the ones we'd scored; reaching \~1000 needs an IT export of the next tranche of customers by spend.
4. **Agree the focus cut:** how tight the named-account book should be (the high-value × high-potential × not-captured intersection).
