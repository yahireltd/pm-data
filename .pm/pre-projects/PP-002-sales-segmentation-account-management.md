---
id: PP-002
slug: sales-segmentation-account-management
title: Sales Segmentation / Account Management
state: planning
created: 2026-06-05T12:25:32Z
updated: 2026-06-09T23:52:32Z
stakeholders:
  - name: Ben
    role: Business SME & Systems Architect
    channel: email
    contact: ben@yahire.com
    internal: true
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
source_tickets: []
summary: |-
  1. Sales Workflow Redesign
  2. Account Segmentation and Management : see freeform
  3. Sales Cadence, Reviews and Enablement TBC
  4. Sales Operations & Sales Franchise Design TBC
  5. Sales processes and principles, account definitions. see freeform quick description
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
scope_in:
  - "The criteria-decisions, settled with the team in scoping sessions: value thresholds, BANT-per-level, and the account-level definitions (system / incubation / account-managed / strategic) — every decision cycle captured as see / decide / act / outcome / review with an owner and a review cadence."
  - "Release strategy + staged shipping of the existing unreleased work (the MlUpdatesClaw / venue-exhibition-hardening line): production-ready slices first (ML scoring pipeline, quote API, AI quote assistant, capacity insights); the beta portals only after their hardening tail, behind gating that does not exist yet."
  - "Lead routing: real-time score written onto each new quote (the existing conversion model wired in), a routing-rules engine mapping score+type to a named conversion process (system-only / quick / in-depth / lifetime / escalate), automated assignment."
  - "Stop-chasing v2, built ON the existing Teresa system: a formal stop state machine (stopped → nurturing → exhausted), multi-touch nurture sequences, and conversion + cost reporting for the stopped pool."
  - "Big-fish alerting: value/potential thresholds that alert a manager on arrival, and a manager worklist held until a convert-or-cease decision is recorded."
  - Account ownership levels layered on the existing customer-tier system, actual-vs-potential value tracking, and the quarterly transfer-window reassignment ritual.
  - "System-estate stewardship: a named non-salesperson steward for system-managed revenue, with pool-level KPIs (closing rate, repeat rate) and a regular results review."
  - "Review loops + dashboards: leakage and process-conformance views, each tied to the named decision it drives (the T-0322 decision-cycle method); reviews operating at company, steward and individual level."
  - "The people/process side of the above: ownership requirements, role definitions and review cadences (the sales system includes how people operate, not just screens)."
scope_out:
  - Building the Yamembership product — only the go/no-go DECISION and its requirements are in scope here; the build is its own project.
  - Sales franchise / compensation design (listed TBC in the summary) — commission and reward structures get decided separately once ownership levels exist.
  - "The logistics branch (PickingSketchSalesDashFriday: route planner, picking decoupling) — a separate release track; only its merge-window sequencing is coordinated with this project."
  - New ML model development — this project integrates the models that already exist (conversion scoring, forecasting); training new model families is out.
  - Replacing Teresa or buying/building a CRM — we extend what exists, not rewrite it.
  - The exhibition portal's Xero reconciliation gap — tracked inside the release-strategy work item, not a separate build here.
success_criteria:
  - "Lead routing live: every new account is auto-scored and routed; score accuracy and conversion-by-route are reviewed on a set cadence, and human corrections feed back into the scoring."
  - "Stop-chasing works: quotes a salesperson stops are picked up by system follow-up and still convert at near-zero handling cost; the stopped-pool conversion rate is reviewed."
  - "Big-fish alerting: high-potential arrivals alert a manager immediately and stay on a worked list until a convert-or-cease decision is recorded."
  - "Account levels live: every assigned account carries a level and an actual-vs-potential position; quarterly transfer windows demonstrably move accounts up and down."
  - "System estate owned: a named non-salesperson steward owns system-managed revenue with KPIs (closing rate, repeat rate) reviewed regularly."
  - "Conformance visible: leakage / process-conformance dashboards exist and are reviewed in management meetings — each one tied to the decision it exists to drive."
cost_of_inaction: |-
  We will not be maximising the potential of each of our customers.
  Risk of losing key business if not managed properly
  Sales effort may be focused on customers / contracts that are not worth focusing on whist other worthwhile customers / contracts are brushed aside
milestones:
  - title: Scoping sessions held — criteria decided
    acceptance_criteria:
      - Value thresholds and BANT-per-level agreed with the team and written down
      - Account-level definitions (system / incubation / account-managed / strategic) agreed
      - Each of the six decision cycles documented as see / decide / act / outcome / review with a named owner and review cadence
  - title: Unreleased-work release strategy executed (slice 1 live)
    acceptance_criteria:
      - "Slice plan agreed: what ships in what order, with gating and rollback for each slice"
      - Production-ready slices (ML scoring pipeline, quote API, AI assistant, capacity insights) merged onto master and live
      - Beta portals remain gated until their hardening checks pass (runtime test, login provisioning, Xero decision)
  - title: Lead routing live
    acceptance_criteria:
      - Every new quote carries a real-time score and is routed to a named conversion process
      - Assignment to the right person/pool happens automatically from the routing rules
      - Conversion-by-route is visible on a dashboard and reviewed on the agreed cadence
  - title: Stop-chasing v2 live
    acceptance_criteria:
      - Stop state machine live on top of Teresa (stopped → nurturing → exhausted) with multi-touch sequences
      - Stopped-pool conversion rate and handling cost reported and reviewed
  - title: Account levels + transfer windows operating
    acceptance_criteria:
      - Every assigned account carries an ownership level and an actual-vs-potential position
      - "Big-fish alerting live: managers alerted on threshold arrivals and the worklist is held to convert-or-cease"
      - The first quarterly transfer-window review has been held and accounts demonstrably moved levels
  - title: Steward + review loops operating
    acceptance_criteria:
      - A named non-salesperson steward owns system-managed revenue with pool KPIs
      - Leakage and conformance dashboards exist, each tied to its named decision, and are reviewed in management meetings
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
kickoff_held: true
scoping_held: true
owner:
  kind: human
  name: Ben
version: 3
---

# PP-002: Sales Segmentation / Account Management

## Summary

1. Sales Workflow Redesign
2. Account Segmentation and Management : see freeform
3. Sales Cadence, Reviews and Enablement TBC
4. Sales Operations & Sales Franchise Design TBC
5. Sales processes and principles, account definitions. see freeform quick description

## Kickoff

_Capture the kickoff agenda + outcomes here, then mark "kickoff held" in planning._

## Planning

_Problem, scope in/out, success criteria, milestones — fill these in before converting._
