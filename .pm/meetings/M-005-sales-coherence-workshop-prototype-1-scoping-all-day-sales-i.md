---
id: M-005
slug: sales-coherence-workshop-prototype-1-scoping-all-day-sales-i
title: Sales Coherence workshop — Prototype 1 scoping (all-day, sales + IT)
state: scheduled
created: 2026-06-09T23:56:35Z
updated: 2026-06-10T00:10:32Z
scheduled_at: 2026-06-12T08:00:00Z
duration_minutes: 480
location: Yahire office (all-day workshop)
project: null
pre_project: PP-002
organizer:
  kind: human
  name: Axel Ferreyrolles
stakeholders:
  - name: Austin
    channel: email
    contact: austin@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-04T01:26:11Z
    role: Dev
    notify_on:
      - comment_added
      - state_change
      - meeting_held
      - outcome_recorded
      - assigned
      - meeting_scheduled
  - name: ben
    channel: email
    contact: ben@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-06T01:44:37Z
    role: SME
agenda:
  - topic: "Definitions table: customer segments + how each is detected + who confirms (Prototype 1 pre-requisite — unblocks the 2–4 week quick win)"
    duration_min: 90
  - topic: Value thresholds + account-level definitions (system / incubation / account-managed / strategic) and ownership requirements per level
    duration_min: 90
  - topic: "Decision cycles: assign a review owner + cadence to each of the six cycles (no loop without a named reviewer)"
    duration_min: 60
  - topic: "Prototype 2 (Governance & Roles) — fill the template: owner, business owners, quick win, prerequisites"
    duration_min: 60
  - topic: "IT capacity ranking: release the built-but-unreleased capabilities vs build the new — ranked knowingly"
    duration_min: 45
  - topic: Agree tracking + convert PP-002 to the live project (all gates green)
    duration_min: 30
outcomes:
  - description: Ben to lead up governance and roles (new sales segmentation pre project)
    recorded_at: 2026-06-10T00:07:44Z
attachments: []
calendar:
  graph_event_id: null
  ics_url: null
kind: scoping
version: 5
---

# Workshop prep — IT readiness pack

*Prepared by Austin (with Claude), 10 Jun 2026. Companion to the Apr 29 Humanness workshop summary.*

## Where we are

The 29 Apr workshop selected **Prototype 1: "Handover Process & Customer-Type Flow / Codified Customer Handling"** — Ben (project), Sam (business), Austin (technical) — quick win *"detect the segments of the customer + tick-box availability"*, pre-requisite *"Customer Definitions & How to detect them."* Since then: Ben's Freeform board designed the target flow in detail; it has been formalised as this pre-project's charter (goals, success criteria, scope, six milestones — all gates green); and IT completed a system gap analysis against what exists and what sits built-but-unreleased.

## What IT brings

**Already live in the system:**
- **Teresa** — automated follow-ups on unconverted/stopped quotes (hourly, frequency caps, A/B-tested discounts). Most of the board's "system-only follow-up" lane runs today.
- Stop-quote infrastructure (reasons, could-still-go-ahead, can-still-email).
- **Customer tiers** with change history — the skeleton for account levels.
- Lead-scoring foundations + a trained quote-conversion model (offline) + rich sales dashboards.

**Built but unreleased:**
- Conversion scoring v2 (materially stronger) + quote prioritisation views.
- Claude-powered quote assistant + quote API + embeddable quote widget.
- Venue / exhibition / account self-service portals (venue piece assessed shippable).
- Daily AI insight/alert digests (the alerting pattern big-fish handling needs).

**Implication:** much of Prototype 1's enabling tech is integration + release work, not new build. Releasing the unreleased line safely should be an explicit early workstream.

## Quick-win feasibility (2–4 weeks)

Realistic — **if this workshop produces the definitions table.** The system has customer type fields, tiers and scoring signals; the quick win is (1) agree segment definitions + detection from data we hold, (2) surface segment + a confirm/override tick-box on quote/customer screens, (3) log corrections so detection improves. The blocker is definitions, not technology.

## What IT needs from the room

1. **Customer/segment definitions + detection rules** — per segment: what defines it, what data identifies it, who confirms it.
2. **Value thresholds** governing routing (system-only / quick / in-depth / lifetime / escalate) — explicitly open on Ben's board.
3. **Account-level definitions** + ownership requirements per level.
4. **Per decision cycle: review owner + cadence** — a loop without a named reviewer doesn't loop.
5. **Prioritisation**: quick-win scope vs releasing unreleased capabilities — same IT capacity, ranked knowingly.

## Method note (fits Axel's framework)

The April workshop's #1 circled issue was **Visibility**, and its "Explore an Interface" questions map nearly 1:1 onto the decision-cycle scoping method: *Problem → what must we SEE → the DECISION it drives → ACTION → expected OUTCOME → who REVIEWS it* (with "adjust & rerun" vs "re-frame" loops). Every proposed report/screen/alert must name its decision; every cycle leaves with a review owner + cadence. This honours the structure-vs-intuition tension: visibility that serves a named decision is the anti-box-ticking filter.

## Values guardrails (from April)

- *Too process-driven*: the "no decision, no dashboard" filter is the protection.
- *Commission logic vs team spirit*: ownership levels + transfer windows designed with the reward question explicitly parked for the franchise work.
- *Two offices, one team*: review rituals double as cohesion mechanisms — shared meetings, not reports.

---

# System gap analysis — Ben's six decision cycles vs the Yahire system

| # | Cycle | On master today | Built, unreleased | Genuinely missing |
|---|---|---|---|---|
| 1 | Lead scoring + routing | Lead scores, pending-quotes assignment UI, offline conversion classifier | Scoring v2, prioritisation dashboard, quote API + AI assistant | Real-time score on the quote; routing-rules engine; auto-assignment |
| 2 | Stop-chasing handover | **Teresa ≈ 80% of this cycle** | Forecasts to enrich timing | Stop state machine (stopped→nurturing→exhausted), multi-touch sequences, stopped-pool reporting |
| 3 | Big-fish alerting | Key-account request stub | Daily AI alert/insight infrastructure (pattern reusable) | Threshold alerts on arrival; manager worklist to convert-or-cease |
| 4 | Segmentation + levels | Customer types, **tiers + change log** | Venue cohort views | Ownership vocabulary + workflow; actual-vs-potential; transfer windows |
| 5 | System estate + remarketing | Teresa for unassigned; campaign infra | **Self-service portals, quote widget, AI assistant** | Membership product (nothing exists); pool KPIs; named steward |
| 6 | Dashboards / review | Rich sales dashboard; ML prediction-vs-actual | 5 capacity dashboards + daily digests | Leakage drill; conformance scorecards; funnel view |

**Strategic findings:** (1) the plan is closer to reality than the board assumes — much is integration/formalisation, not greenfield; (2) months of directly-relevant value sits unreleased and ageing — releasing it IS sales-plan work; (3) the genuinely-new build list reduces to: routing engine + real-time scoring hookup, stop state machine on Teresa, threshold alerting + manager worklist, ownership levels + transfer windows on tiers, steward KPIs + membership decision, leakage/conformance views.

## Suggested workshop outputs ("done" for the day)

1. The definitions table (segments + detection + confirm flow) — unblocks the quick win immediately.
2. Thresholds + account levels agreed (or parked with owners + dates).
3. Prototype 2 template filled (Governance & Roles).
4. Each decision cycle assigned a review owner + cadence.
5. Ranked call on release-the-built vs build-the-new.
6. PP-002 converted to the live project.


---

# IT briefing addendum (Austin — pre-meeting)

## Translation table: Humanness language ↔ our work

| Axel says | It means | Maps to |
|---|---|---|
| Sales Engine | The operating *structure* — 10 nodes (opportunity flow, sales ops, IT/tools, roles, visibility, commission, communication, enablement, governance, segmentation) | The system + process work — this pre-project's scope |
| Sales Franchise | The *leadership practice* — manager cadences, reviews, feedback, people development | The review loops in the decision cycles; the cadences Nathan + Sam drafted in April |
| Coherence | Components *and the relationships between them* working as one | The "every screen ties to a named decision" filter |
| Prototype / quick win | 3-month testable pilot / 2–4 week first move | Milestones 1–2 |
| The chocolate metaphor | Structure is the crust protecting the intuitive "melty core" — not replacing it | The anti-box-ticking guardrail |

## IT's position, precisely

Technical owner of Prototype 1. The quick win (segment detection + confirm tick-box) is **feasible in 2–4 weeks — counted from the definitions table, not from today**. The build shape: definitions agreed → segment + confirm/override surfaced on quote/customer screens → corrections logged so detection improves (that correction loop is also the seed of ML routing later). No date commitments on anything downstream (routing, levels, transfer windows) until definitions + thresholds exist.

## Landmines and the path past them

- **Structure vs intuition** — the room's deepest anxiety. Framing: the decision-cycle filter (*no dashboard without a named decision*) is the **protection against** box-ticking, not the source of it.
- **Commission/reward** — ownership levels will instantly raise "what does this mean for my commission?" Park it explicitly: reward design belongs to the Franchise work, later.
- **Two offices, one team** — pitch review rituals (transfer windows, steward reviews) as *shared meetings*, a cohesion mechanism, not reports read alone.
- **Over-promising** — the unreleased portals are real but: exhibition needs a runtime test + has a Xero gap; account portal can't provision logins yet. Say "built and close", not "ready tomorrow".
- **The Teresa reveal** — when it lands that Teresa already covers most system follow-up, the answer to "so what's missing?" is: formal stop states, multi-touch sequences, and reporting on the stopped pool.

## Likely questions → answers

- *"Can the system tell who's a corporate repeat customer?"* → The signals exist (type fields, history, scoring). What it can't do is apply **your** definitions — that's today's job.
- *"How long until we see something?"* → Quick win 2–4 weeks **from the definitions table**.
- *"Can AI do the routing?"* → The model exists (a stronger one unreleased). Honest sequence: rules from your thresholds first, the model assists, humans override, overrides train it.
- *"What's this costing IT?"* → Reframe to the ranking decision: much of the value is already built — choose between *releasing it* and *building new* (agenda item 5).
- *"Where is all this tracked?"* → This pre-project (PP-002); the April workshop and today are recorded as meetings with outcomes; it converts to the live project when this room says go.

**The one-line version of the day for IT: walk in with the gap analysis, walk out with the definitions table — everything else is sequencing.**
