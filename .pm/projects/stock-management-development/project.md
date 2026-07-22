---
id: P-0021
slug: stock-management-development
name: Stock Management Development
state: active
phase: planning
created: 2026-07-13T11:14:18Z
updated: 2026-07-22T08:32:48Z
owner:
  kind: human
  name: you
goal: |-
  One coherent stock-management area where: quality is graded on a consistent, future-proof scale with multiple photos and actionable failure points; buffer stock + set
    stock levels drive automatic shortage/replenish alerts (no more manual "do we have enough?" checks); upcoming stock, suppliers and orders are managed from a
    consolidated view; and staff can see quality levels and stock decisions across all products and action them (replace / buy more / remove) from one place.
success_criteria: []
parent_project: null
related_projects: []
parent_ticket: null
key_decisions: []
labels: []
order: 1024
problems:
  - |-
    Stock/inventory management in Yasystem has grown piecemeal and now has real gaps. Quality grading is limited (one photo per grade, no consistent scale, failure points
      are just free text). Sale-stock quantities can't be manually corrected. There's no proper buffer-stock concept — "upcoming stock" is being used as a stop-gap — and no
      set stock levels or shortage alerting, so the team constantly checks "do we have enough?" by hand. Upcoming stock, suppliers and orders are fragmented across tabs with
      no consolidated view. The result is missed/late shortages, inconsistent and undocumented quality decisions, and no data to act on — and this manual load grows with the
      stock base.
goals:
  - |-
    One coherent stock-management area where: quality is graded on a consistent, future-proof scale with multiple photos and actionable failure points; buffer stock + set
      stock levels drive automatic shortage/replenish alerts (no more manual "do we have enough?" checks); upcoming stock, suppliers and orders are managed from a
      consolidated view; and staff can see quality levels and stock decisions across all products and action them (replace / buy more / remove) from one place.
cost_of_inaction: |-
  The team keeps manually checking stock levels and correcting quantities, shortages get caught late or missed (risking lost bookings), quality decisions stay
    inconsistent and undocumented, and there's no data to project losses or optimise. As the stock base grows, this manual overhead and shortfall risk only increase, and
    Sandor/workshop keep relying on ad-hoc checks with Ben/IT firefighting.
kind: initiative
version: 116
scope_in:
  - Quality management (grading scale, multiple photos, failure points, quality overview & history)
  - Sale stock manual quantity adjustment
  - Suppliers (edit supplier items, consolidated orders view)
  - Buffer stock + set stock levels + shortage/replenish alerts
  - Upcoming stock & order-management rework
  - YaBundles (populate + small tweaks)
scope_out:
  - Venue Database → cart postcode auto-detection ("is your event at ExCel?") — separate ticket
  - Revenue optimisation — likely stays with Austin (TBC)
  - Full stock expiration / end-of-life tracking across large mixed lines — later spike
  - Loss projections modelling — later, ties into capacity utilisation
repo_url: https://github.com/yahireltd/Ya-Hire-Management
branch: Stock-Management-Development
agent_policy:
  allow_commit: false
  allow_push: false
---

# Stock Management Development

## Overview

_What this project is about._

## Why now

## Design

## Milestones
