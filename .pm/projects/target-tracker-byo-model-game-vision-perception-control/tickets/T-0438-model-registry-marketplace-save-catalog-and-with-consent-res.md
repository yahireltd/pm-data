---
id: T-0438
title: "Model registry + marketplace: save, catalog, and (with consent) resell user-trained per-game models"
type: feature
state: triaged
created: 2026-06-19T03:19:09Z
updated: 2026-06-19T03:19:09Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - A model registry stores every trained model with weights hash + data version + metrics + lineage + ownership/licence + a resellable flag
  - An ADR defines model ownership, user consent, and resale/revenue terms
  - resellable is set automatically from data provenance (clean-source only; scraped-data models flagged personal-use)
  - Catalogued per-game models are reusable by new users (download or fine-tune-from) and feed the base model
  - The catalog stays within the single-player/offline + accessibility/QA scope (ADR-001)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Idea (owner direction, 2026-06-19)
Don't just train one-off models — SAVE every user-trained per-game model into a catalog and (with consent) resell them. Each game accumulates a best-in-class model; new users for that game get it instantly or fine-tune from it.

## Why it's a moat (the flywheel)
More users → more per-game models + their labeled data → a better BASE model → easier/cheaper next-game training → more users. Every user model is also training data for the base, so the marketplace and the base model (MS-009) reinforce each other.

## What it needs
- **Model registry as the backbone** (already in MS-007): one record per model with weights hash, data version, metrics, lineage — extend with ownership, licence, and a `resellable` flag.
- **Consent + ownership terms** (NEW — needs an ADR): users opt in (revenue-share, or free-tier-in-exchange) to us storing/sharing/reselling models they train; define who owns a user-trained model and the resale terms.
- **Clean-provenance gates resale** (ADR-006): provenance.py records each model's data lineage; a model is auto-marked resellable ONLY if its data is clean (capture/licensed/synthetic). Scraped-data models = personal/non-redistributable use only.
- **Scope** (ADR-001): the catalog stays single-player/offline + accessibility/QA/research framed.

## Dependencies / fit
Builds on MS-007 (model registry) + MS-009 (base model + per-game specialists) + MS-010 (productize). Each catalogued per-game model also feeds the base via active learning.