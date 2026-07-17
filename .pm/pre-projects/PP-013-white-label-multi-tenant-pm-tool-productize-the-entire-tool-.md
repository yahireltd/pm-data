---
id: PP-013
slug: white-label-multi-tenant-pm-tool-productize-the-entire-tool-
title: White-label, multi-tenant pm-tool — productize the ENTIRE tool as a production-ready SaaS others run under their own brand
state: draft
created: 2026-07-17T22:01:41Z
updated: 2026-07-17T22:11:03Z
stakeholders: []
source_tickets: []
summary: |-
  Not a module — the whole tool. Turn pm-tool itself into a white-label, multi-tenant, production-ready product that other teams / agent-first builders run (potentially under their own brand). The meeting-intelligence app (PP-012) is ONE capability inside this platform, not the product itself.

  Vision (Austin, 2026-07-17): pm-tool is for the agent-first builder — team-scale complexity, solo bandwidth — and its real value is a CONTEXT LEDGER for agent collaboration + forcing/teaching good PM practice + judgment guardrails (not ceremony). Productizing it means anyone in that position can run their own instance.

  Today's reality: pm-tool is single-tenant and file-based (a per-deploy .pm markdown repo, driven via remote MCP + web UI), built for Austin. To be a product others use it needs: accounts/auth, tenant isolation, billing/subscriptions, white-label branding, onboarding, production hardening, and a decision on data architecture.

  KEY ARCHITECTURE FORK (the big open decision): deploy model — (a) per-customer ISOLATED environments (extends today's .pm-per-deploy model; trivial isolation incl. biometric voiceprints, heavier ops, awkward central billing/analytics) vs (b) CENTRALISED multi-tenant DATABASE (one system, easy billing/limits/updates, but you own tenant isolation as a hard security problem and the file-based .pm model must become a real database). Austin's earlier lean noted: a possible HYBRID — centralised control plane (auth/billing/usage-limits) + isolated per-tenant data stores — keeps the simple file model + strong isolation while giving central metering. Decide with a clear head.

  Other threads: subscription tiers with compute-driven limits (audio minutes/file size/meeting count — transcription burns GPU); how much of the current MCP/agent-workflow surface is exposed per tenant; migration path from the current single live instance. Relationship: PP-012 (meeting app) plugs in as a module; this platform is its home.
owner:
  kind: human
  name: Austin
version: 4
goals:
  - "⭐ MEETING INTELLIGENCE — THE FLAGSHIP. Record a meeting on your phone and it becomes a named, speaker-attributed transcript → draft decisions/outcomes → tracked, owned tickets — automatically. Reliable 'who said what / who DECIDED what', even for sound-alike voices (proven today: separated two brothers who sound alike, where off-the-shelf tools would mislabel them). Meetings are where decisions get made and lost; this turns them from a black hole into a structured, attributed, actionable record — with ZERO discipline required. It's also the on-ramp to every other benefit: the capture that feeds the context ledger happens for free, just by recording."
  - "⭐ UI-BASED AGENT MANAGEMENT, AT THE PROJECT LEVEL — a visual control tower for the agents working a project: see every agent's activity (claimed / in progress / in review), watch live run progress, and dispatch, monitor and review their work from one project dashboard instead of scattered terminal sessions. This is the cockpit for the 'team-scale complexity, solo bandwidth' operator — directing a workforce of agents needs a management surface, and doing it per project keeps each effort's agent activity coherent, visible and reviewable in one place."
  - CONTEXT THAT CARRIES ACROSS AGENT SESSIONS — the record of what was tried, why, and how we got here, so every agent session starts informed instead of re-deriving. 'Working with the agent, context is everything.'
  - KEEPS THE HUMAN IN THE JUDGMENT SEAT — the claim→review→test→close discipline forces the operator to verify and decide, protecting against deskilling into a passive prompt-conduit ('don't become just a prompt engineer').
  - TEACHES GOOD PM BY DOING — opinionated, forcing gates scaffold sound practice for people learning PM on the go (developer → technical owner).
  - NOTHING GETS LOST OR LEFT UNFINISHED — makes agent work visible and hard to abandon mid-flight; shipped-but-unclosed work is caught.
  - AIMED AT THE REAL BOTTLENECK — organises the decisions, meetings, testing and feedback that actually stall projects once agents write the code.
success_criteria:
  - A fresh agent session can pick up an in-flight project from the record ALONE — what was tried, why, current state — and act correctly without a human re-brief.
  - Shipped work does not sit unverified or unclosed in practice — the close-the-loop gap is actually closed, not just nudged.
  - A user new to PM produces sound artifacts (charter, scope, test plans, decisions-with-rationale) simply by following the tool.
  - Meeting decisions reliably become tracked, owned work with no manual re-entry.
  - "Honest positioning holds: described as 'makes the right things impossible to lose and hard to leave unfinished' — NOT 'does the deciding or testing for you'. The tool ORGANISES and SURFACES the new bottleneck; it does not dissolve it."
---

# PP-013: White-label, multi-tenant pm-tool — productize the ENTIRE tool as a production-ready SaaS others run under their own brand

## Summary

Not a module — the whole tool. Turn pm-tool itself into a white-label, multi-tenant, production-ready product that other teams / agent-first builders run (potentially under their own brand). The meeting-intelligence app (PP-012) is ONE capability inside this platform, not the product itself.

Vision (Austin, 2026-07-17): pm-tool is for the agent-first builder — team-scale complexity, solo bandwidth — and its real value is a CONTEXT LEDGER for agent collaboration + forcing/teaching good PM practice + judgment guardrails (not ceremony). Productizing it means anyone in that position can run their own instance.

Today's reality: pm-tool is single-tenant and file-based (a per-deploy .pm markdown repo, driven via remote MCP + web UI), built for Austin. To be a product others use it needs: accounts/auth, tenant isolation, billing/subscriptions, white-label branding, onboarding, production hardening, and a decision on data architecture.

KEY ARCHITECTURE FORK (the big open decision): deploy model — (a) per-customer ISOLATED environments (extends today's .pm-per-deploy model; trivial isolation incl. biometric voiceprints, heavier ops, awkward central billing/analytics) vs (b) CENTRALISED multi-tenant DATABASE (one system, easy billing/limits/updates, but you own tenant isolation as a hard security problem and the file-based .pm model must become a real database). Austin's earlier lean noted: a possible HYBRID — centralised control plane (auth/billing/usage-limits) + isolated per-tenant data stores — keeps the simple file model + strong isolation while giving central metering. Decide with a clear head.

Other threads: subscription tiers with compute-driven limits (audio minutes/file size/meeting count — transcription burns GPU); how much of the current MCP/agent-workflow surface is exposed per tenant; migration path from the current single live instance. Relationship: PP-012 (meeting app) plugs in as a module; this platform is its home.

## Kickoff

_Forced before convert-to-project (later slice)._

## Planning

_Forced before convert-to-project (later slice)._
