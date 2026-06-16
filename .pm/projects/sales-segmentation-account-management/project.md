---
id: P-0018
slug: sales-segmentation-account-management
name: Sales Segmentation / Account Management
state: planning
phase: build
created: 2026-06-16T17:34:29Z
updated: 2026-06-16T18:56:24Z
owner:
  kind: human
  name: Ben
goal: |-
  1. Sales Workflow Redesign
  2. Account Segmentation and Management : see freeform
  3. Sales Cadence, Reviews and Enablement TBC
  4. Sales Operations & Sales Franchise Design TBC
  5. Sales processes and principles, account definitions. see freeform quick description
success_criteria:
  - "Lead routing live: every new account is auto-scored and routed; score accuracy and conversion-by-route are reviewed on a set cadence, and human corrections feed back into the scoring."
  - "Stop-chasing works: quotes a salesperson stops are picked up by system follow-up and still convert at near-zero handling cost; the stopped-pool conversion rate is reviewed."
  - "Big-fish alerting: high-potential arrivals alert a manager immediately and stay on a worked list until a convert-or-cease decision is recorded."
  - "Account levels live: every assigned account carries a level and an actual-vs-potential position; quarterly transfer windows demonstrably move accounts up and down."
  - "System estate owned: a named non-salesperson steward owns system-managed revenue with KPIs (closing rate, repeat rate) reviewed regularly."
  - "Conformance visible: leakage / process-conformance dashboards exist and are reviewed in management meetings — each one tied to the decision it exists to drive."
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
  - Every customer has an accountable owner — human, or the named steward of the system-managed pool. No unowned revenue.
  - Every new lead is scored and routed to a right-sized conversion process (system-only / quick / in-depth / lifetime / manager escalation) instead of uniform handling.
  - Every assigned account sits at a defined level (system / incubation / account-managed / strategic) with visible actual-vs-potential momentum, governed by quarterly transfer windows.
  - Every screen, score or dashboard we build is tied to a named decision, the action it drives, and a review loop — the decision-cycle scoping method (T-0322). Visibility with no decision attached doesn't get built.
  - "Results are reviewed and iterated routinely at three levels: company/trend, system steward, and individual."
cost_of_inaction: |-
  We will not be maximising the potential of each of our customers.
  Risk of losing key business if not managed properly
  Sales effort may be focused on customers / contracts that are not worth focusing on whist other worthwhile customers / contracts are brushed aside
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
version: 18
agent_policy:
  allow_commit: false
  allow_push: false
repo_url: https://github.com/yahireltd/Ya-Hire-Management
risks:
  - description: Large Project on existing system has integration risks
    severity: high
    mitigation: Work on branches, make sure we merge master over branch before merging so we do not loose any work / break anything that is already working
---

# Sales Segmentation / Account Management

## Overview

Converted from pre-project PP-002.

## Why now

## Design

## Milestones
