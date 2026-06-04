---
id: ADR-007
slug: agents-follow-a-pm-hygiene-ruleset-agents-md
title: Agents follow a PM-hygiene ruleset (AGENTS.md)
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets: []
---

## Context
Dogfooding pm-tool surfaced a failure mode: a lot of work (decisions, docs, status, tech-sessions, milestones) shipped without being captured in the tool. The "why" lived only in commit messages and chat. Force-gates catch unrecorded *tickets* but not unrecorded *decisions/records*.

## Decision
Codify a PM-hygiene ruleset in AGENTS.md at the repo root (auto-read by Claude Code / Cursor / Codex). It makes explicit, per record type, what an agent must do to keep the record current: claim before coding, record progress, complete-run with a test plan, write an ADR for significant decisions, log tech-sessions, record outcomes + milestones, write status notes, keep docs/refs/code_anchors travelling with the change, and never route around a gate (add the missing tool instead).

## Consequences
+ One canonical capture-as-you-go contract; the next agent picks up cold.
+ References the write-parity tools this gap forced us to build (pm_update_ticket, pm_create_milestone, pm_create_decision).
- Currently documented, not enforced. Follow-up: turn enforceable rules into lint gates.