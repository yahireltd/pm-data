---
id: ADR-006
slug: data-sourcing-licensing-provenance-and-dataset-versioning-po
title: Data sourcing, licensing, provenance, and dataset-versioning policy
state: accepted
decided: 2026-06-19
decided_by:
  - austin
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets: []
version: 2
---

## Context
The released-tooling direction makes datasets and weights potentially distributable artifacts whose provenance determines legal exposure. Owner decided (2026-06-19) to ALLOW limited web scraping as a data source, alongside capture/licensed/synthetic.

## Decision
Source tiers, with a redistribution boundary:
- self-captured gameplay, explicitly-licensed/CC/public-domain, and engine-rendered synthetic = clean tier, usable in REDISTRIBUTABLE artifacts (a published base model).
- limited web-scraped gameplay behind the CLIP domain gate = ALLOWED for NON-redistributable / research / personal datasets and as a labeling/bootstrap input, but NOT for redistributable artifacts.
Rule: CLIP domain-match != license clearance. The provenance manifest records a source tier per image, so a "redistributable" base build automatically filters to the clean tier. HL/Xash: owned game data used locally, not redistributed. Versioning: content-hash + provenance manifest (DVC/git-lfs as future option).

## Consequences
Fits the research/hobbyist beachhead (ADR-008), where personal/research use of scraped frames is more defensible. The qualify funnel must tag source tier; base-build refuses non-clean tiers. Owner still to confirm which commercial single-player titles may be captured.