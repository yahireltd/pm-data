---
id: P-0018
slug: sales-segmentation-account-management
name: Sales Segmentation / Account Management
state: planning
phase: build
created: 2026-06-16T17:34:29Z
updated: 2026-07-03T02:02:59Z
owner:
  kind: human
  name: Ben
goal: Transform Yahire's sales into a scalable, measured machine — every customer owned, every lead right-sized, every account at a defined level, and every dashboard tied to a decision — delivered in four phases (Efficiency → Operating-System Foundation → Management & Learning → Strategic Account Platform) on Yasystem.
success_criteria:
  - New enquiries are scored and routed automatically to the right process, the right person or pool picks them up, and we review how well the scoring and routing are working — and improve them.
  - When a salesperson stops chasing a quote, the system keeps following up and still wins some of them at almost no cost — and we can see how many.
  - When a big opportunity comes in, a manager is told straight away and it stays on their list until they decide to chase it or let it go.
  - Every managed account has a level and a 'worth now vs could be worth' figure, and the quarterly review actually moves accounts up and down.
  - A named non-salesperson owns the customers the system handles, with simple measures (how often we win, how often they come back) reviewed regularly.
  - We have dashboards showing where opportunities are being lost or the process isn't being followed, reviewed in management meetings — each one there to drive a specific decision.
  - The finished-but-unused work is live (the scoring, the quote tools, the AI quote assistant and the capacity views), and the busy sales screens are noticeably faster and simpler.
parent_project: null
related_projects: []
parent_ticket: null
parent_pre_project: PP-002
kind: initiative
key_decisions: []
labels: []
order: 1024
problems:
  - These are not values, these are points that specifically aim to transform where we are now in a state of growth with pockets of incoherence into a scalable sales machine that manages all opportunity and activity to the maximum whilst delivering performance insights, feedback loops and personal development.
  - To be clear our sales system includes how people operate and how we manage, not just what is on our screen.
  - 1.	Every customer requires clear ownership of someone who’s job it is to maximise
  - (this includes unassigned/system handled)
  - 2.	We design the process and the roles to maximise customer outcomes, not the other way round
  - (if we make more money by assigning someone, we must make sure we have enough)
  - 3.	Our job is to maximise the lifetime value of opportunities
  - (Inbound as a closing-only, top accounts and sporadic AM coverage is not sufficient)
  - 4.	Sales execs are not just closers they’re opportunity identifiers and incubators
  - (Sell on trust and care)
  - 5.	Venues are mostly referral accounts
  - (we also maximise the customers they refer)
  - 6.	We review results and innovate at every level, routinely.
  - (At the company/trend level, system manager and individual level)
  - 7.	We systematically develop people as well as customers
  - (‘becoming the best versions of ourselves’ is aiming for something tangible and improving who we were yesterday)
  - 8.	Things automate and systemise, but people by from people
goals:
  - Every customer has someone responsible for them — a salesperson, or a named person who looks after the ones the system handles. No customer is left with nobody.
  - Every new enquiry is automatically sized up and sent down the right path — handled by the system, a quick reply, a fuller sell, long-term account care, or passed up to a manager — instead of treating everyone the same.
  - Every account we look after has a clear level (system-handled, being grown, account-managed, or strategic) and a simple 'worth now vs could be worth' figure, and we review who looks after what every quarter.
  - Anything we put on a screen exists to help someone make a decision and take action — and we check whether it did. If a screen doesn't change what we do, we don't build it.
  - We review results and improve at three levels — the whole company, the person running the system-handled pool, and each individual.
  - Switch on the value we've already built and cut the everyday hassle — release the finished-but-unused work and make the busy sales screens faster and simpler, so the same team gets more done with less effort.
cost_of_inaction: "If we don't do this: we keep leaving money on the table with our existing customers; we risk losing important accounts because no one is clearly responsible for them; and we keep spending sales time on customers who don't really need it while better opportunities get ignored."
scope_in:
  - "The criteria-decisions, settled with the team in scoping sessions: value thresholds, BANT-per-level, and the account-level definitions (system / incubation / account-managed / strategic) — every decision cycle captured as see / decide / act / outcome / review with an owner and a review cadence."
  - "Release strategy + staged shipping of the existing unreleased work (the MlUpdatesClaw / venue-exhibition-hardening line): production-ready slices first (ML scoring pipeline, quote API, AI quote assistant, capacity insights); the beta portals only after their hardening tail, behind gating that does not exist yet."
  - "Lead routing: real-time score written onto each new quote (the existing conversion model wired in), a routing-rules engine mapping score+type to a named conversion process (system-only / quick / in-depth / lifetime / escalate), automated assignment."
  - "Stop-chasing v2: FIRST an effectiveness review of the existing Teresa follow-up automation — it runs, but it has not delivered results — and an explicit extend-or-replace decision made on data (stopped-pool conversion + cost). Then the formal stop state machine (stopped → nurturing → exhausted), multi-touch sequences, and the stopped-pool reporting whose absence let the ineffectiveness go unnoticed for years."
  - "Big-fish alerting: value/potential thresholds that alert a manager on arrival, and a manager worklist held until a convert-or-cease decision is recorded."
  - Account ownership levels layered on the existing customer-tier system, actual-vs-potential value tracking, and the quarterly transfer-window reassignment ritual.
  - "System-estate stewardship: a named non-salesperson steward for system-managed revenue, with pool-level KPIs (closing rate, repeat rate) and a regular results review — the role whose absence is exactly why Teresa could run ineffectively unnoticed."
  - "Review loops + dashboards: leakage and process-conformance views, each tied to the named decision it drives (the T-0322 decision-cycle method); reviews operating at company, steward and individual level."
  - "The people/process side of the above: ownership requirements, role definitions and review cadences (the sales system includes how people operate, not just screens)."
scope_out:
  - Building the Yamembership product — only the go/no-go DECISION and its requirements are in scope here; the build is its own project.
  - Sales franchise / compensation design (listed TBC in the summary) — commission and reward structures get decided separately once ownership levels exist.
  - "The logistics branch (PickingSketchSalesDashFriday: route planner, picking decoupling) — a separate release track; only its merge-window sequencing is coordinated with this project."
  - New ML model development — this project integrates the models that already exist (conversion scoring, forecasting); training new model families is out.
  - Buying or building a general CRM — out of scope regardless of how the Teresa extend-or-replace decision lands.
  - The exhibition portal's Xero reconciliation gap — tracked inside the release-strategy work item, not a separate build here.
workstream_ownership:
  - workstream: Programme lead / facilitation (Humanness 'Sales Coherence' journey)
    owner: Axel Ferreyrolles
  - workstream: Prototype 1 — Handover process & customer-type flow / codified customer handling (project owner)
    owner: Ben
  - workstream: Prototype 1 — business ownership
    owner: Sam
  - workstream: Prototype 1 — technical ownership (Yasystem integration, quick-win build)
    owner: Austin
  - workstream: Prototype 2 — Governance & roles/responsibilities (to be created Thu 12 Jun)
    owner: TBC at workshop
  - workstream: Sales franchise / manager cadence design
    owner: Nathan + Sam (drafted at Apr workshop)
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
  - name: Zsolt
    role: Developer & System SME
    channel: email
    contact: zsolt@yahire.com
    internal: true
  - name: Taran
    role: Sales & Marketing SME
    channel: email
    contact: taran@yahire.com
    internal: true
  - name: Nathan
    role: Sales Manager SME
    channel: email
    contact: nathan@yahire.com
    internal: true
  - name: Sam
    role: Business Development Manager SME
    channel: email
    contact: sam@yahire.com
    internal: true
  - name: Thanos
    channel: email
    contact: athanasios@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-06T01:25:30Z
    role: Marketing / Sales Ops
version: 248
agent_policy:
  allow_commit: false
  allow_push: false
repo_url: https://github.com/yahireltd/Ya-Hire-Management
risks:
  - description: Large Project on existing system has integration risks
    severity: high
    mitigation: Work on branches, make sure we merge master over branch before merging so we do not loose any work / break anything that is already working
project_type: non-tech
---

# Sales Segmentation / Account Management

## Overview

Make Yahire's sales work like a well-run machine instead of relying on memory and luck. The aim: every customer has someone responsible for them, every enquiry gets the right amount of attention for what it's worth, and we can see what's working and fix what isn't. "Sales" here means how our people work and how we manage — not just the screens. We'll deliver it in four phases, all built on our existing Yasystem:

1. **Sales Efficiency** — tidy up and speed up the everyday work.
2. **Sales Operating System Foundation** — build the engine that sends the right customer to the right place.
3. **Management & Learning System** — dashboards and reviews so managers can improve results.
4. **Strategic Account Platform** — grow our biggest accounts.

## Why now

Three reasons this is the moment:

1. **We're spending effort in the wrong places — and now we can prove it.** Looking at around 31,000 quotes: small repeat customers mostly buy again on their own (today over half of them still take up a salesperson's time for almost no extra gain), while big *new* customers — our hardest and most valuable to win — don't get enough attention. Salespeople clearly earn their keep on new customers; we just need to point them there.
2. **We've already built a lot that isn't switched on yet.** The scoring, the quote tools and the AI quote assistant are built but not released. Much of what this project needs already exists — it just needs turning on and joining up.
3. **We've been flying blind.** Our automatic follow-up has run for nearly two years with nobody checking it — and it turns out it isn't really helping. Without someone owning it and a simple dashboard, that went unnoticed for years.

So now: switch on what we've built, point sales effort where it pays, and put the management and measurement in place — before more good opportunities slip away.

## Design

The plan is delivered as four phases — see the milestones (MS-001 to MS-004), each with its goal, the things we'll actually do, and the outcome. Each phase is delivered through one or more sprints.
