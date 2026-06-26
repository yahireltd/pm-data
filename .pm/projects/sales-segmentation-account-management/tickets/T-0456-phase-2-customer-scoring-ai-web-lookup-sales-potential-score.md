---
id: T-0456
title: Phase 2 · Customer scoring — AI web-lookup sales-potential scores
type: feature
state: triaged
created: 2026-06-22T21:41:31Z
updated: 2026-06-26T14:50:45Z
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
version: 3
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