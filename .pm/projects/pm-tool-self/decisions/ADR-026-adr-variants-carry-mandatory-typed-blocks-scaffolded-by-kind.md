---
id: ADR-026
slug: adr-variants-carry-mandatory-typed-blocks-scaffolded-by-kind
title: ADR variants carry mandatory typed blocks scaffolded by kind
state: accepted
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0153
  - T-0154
  - T-0155
---

## Context
A plain Context/Decision/Consequences ADR is too thin for certain high-risk decision classes — integrations, deployments, test plans, dependencies, and change-control all have a specific section that teams habitually skip and regret. We could have kept one generic ADR template and trusted authors to remember the deployment rollback or the actual test scenarios, or written separate document types entirely.

## Decision
Keep one Decision entity but add a `kind` discriminator (generic/dependency/integration/deployment/test-plan/change-control) where each non-generic kind scaffolds and is expected to carry its load-bearing block: integration => failure modes, deployment => rollback, test-plan => concrete SIT/UAT scenarios + environments, dependency => sharp edges, change-control => time impact in HOURS (never currency). The same per-kind template is emitted identically by web, CLI, and MCP so the mandatory section is always present to fill in.

## Consequences
More schema surface (per-kind sub-objects on decision.schema.json) and a template that must stay identical across three surfaces. The payoff: the section most likely to be skipped is pre-printed and expected for exactly the decision types where omitting it bites — rollback steps exist before cutover, test plans list real scenarios not 'we'll test it', and change-control is denominated in time not money. Variants are additive, so existing generic ADRs are unaffected.