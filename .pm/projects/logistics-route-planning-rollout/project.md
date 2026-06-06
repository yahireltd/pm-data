---
id: P-0007
slug: logistics-route-planning-rollout
name: Logistics Route Planning rollout
state: planning
phase: test
created: 2026-06-04T13:46:09Z
updated: 2026-06-06T20:47:04Z
owner:
  kind: human
  name: you
goal: Needs to be safe to finalise - we do not want to break the run planner / miss any jobs that need to be delivered / collected or allow 2 people editing the same date
success_criteria: []
parent_project: null
related_projects: []
parent_ticket: null
code_surfaces: []
key_decisions: []
labels: []
order: 1024
problems:
  - Automatic route planning for logistics
goals:
  - Needs to be safe to finalise - we do not want to break the run planner / miss any jobs that need to be delivered / collected or allow 2 people editing the same date
cost_of_inaction: Logistics will be manually plannning days and this is very time consuming. Routes will not be as efficient.
risks:
  - description: Data issues when finalising
    severity: medium
    mitigation: Testing all edge cases trying to break it intentionally.
  - description: Missing dels / cols
    severity: medium
success_measures:
  - When logistics are confident enough to start using it
time_budget_hours: 30
go_live_target: 2026-06-30
workflow_impact:
  who_affected:
    - logistics
  current_workflow: All planned manually on the route planner
  new_workflow: |
    Plan on sketch, finalise to make on the route planner. Lock what is good and then replan.
  sources_of_truth: |
    Run planner and sketch planner are both sources of truth currently. The idea is to make them seamlessly work together
scope_in:
  - Bug fixes
  - issues preventing them using it in real world
scope_out:
  - Nice to have features that are not vital to the usage
workstream_ownership:
  - workstream: All
    owner: Austin
repo_url: https://github.com/yahireltd/Ya-Hire-Management
branch: PickingSketchSalesDashFriday
agent_policy:
  allow_commit: true
  allow_push: false
  note: working branch of a production system proceed with caution
---

# Logistics Route Planning rollout

## Overview

_What this project is about._

## Why now

## Design

## Milestones
