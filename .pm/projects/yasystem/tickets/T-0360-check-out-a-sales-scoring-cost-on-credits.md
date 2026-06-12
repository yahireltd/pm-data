---
id: T-0360
title: Check out a sales scoring cost on credits
type: feature
state: in_progress
priority: p2
created: 2026-06-11T23:57:22Z
updated: 2026-06-12T00:12:20Z
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
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
version: 7
branch: refund-hardening-t0331-stripe-dryrun
---

So the idea to score a customer is to first of all use current personal /  coroprate split -this is done using the email domain so non custom domains (gmail/hotmail/outlook.com) are personal customers else corporate. Then we need to identify industry. Look u p the domains website. So for example the customer domain is  customer@excel.london we would send an agent to look at their website. and if found to be a venue / caterer etc then look up their socials and public financial records to asses their potential value, event frequency etc. 

we want to figure out how much this lookup would cost in credits per customer
