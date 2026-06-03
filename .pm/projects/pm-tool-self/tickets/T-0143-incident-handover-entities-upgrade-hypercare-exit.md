---
id: T-0143
title: Incident + Handover entities; upgrade Hypercare exit gate off placeholder
type: feature
state: done
priority: p2
created: 2026-05-29T13:28:10Z
updated: 2026-05-29T15:22:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 15360
reporter: null
assignee: null
acceptance_criteria:
  - Incident and Handover are first-class entities with their own schemas, creatable from the CLI and viewable in the web app
  - The Hypercare exit gate is backed by real entities — exit is blocked until a signed-off handover exists and no high/critical incidents remain open (no more placeholder check)
out_of_scope: []
code_anchors:
  - path: schemas/incident.schema.json
  - path: schemas/handover.schema.json
  - path: web/app/projects/[slug]/hypercare/page.tsx
  - path: .pm/playbook.yml
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## Problem

_What's wrong / what's needed?_

## Context

_Background, screenshots, customer quotes._

## Design notes

_How we'll fix it._
