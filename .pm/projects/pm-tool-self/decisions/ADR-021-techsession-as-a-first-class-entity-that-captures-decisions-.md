---
id: ADR-021
slug: techsession-as-a-first-class-entity-that-captures-decisions-
title: TechSession as a first-class entity that captures decisions, not transcripts
state: accepted
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0156
---

## Context
Working/Claude deep-dive sessions produce decisions and next-actions that otherwise evaporate into chat logs. We could have (a) appended session notes to the related ticket's body or comments; (b) stored raw transcripts as attachments; or (c) relied on agents to file ADRs for everything. Ticket bodies bury the decisions among status; raw transcripts are noise; not every working session rises to an ADR.

## Decision
Add a dedicated TechSession entity (TS-NNN, schemas/tech-session.schema.json, pm_create/record/get/list_tech_session). Its shape enforces the discipline: state the `problem` + `success_criterion` up front, then capture `decisions[]` and `actions[]` (each action may link a follow-up ticket), and park `side_quests[]` to route to the backlog rather than absorb into scope. It is explicitly NOT a transcript store — there is no field for raw conversation.

## Consequences
Another entity type and storage path to maintain, and a judgement call for contributors about session-vs-ticket-vs-ADR. But decisions made during deep work become durable, searchable records with named success criteria and follow-up linkage, and the schema actively discourages dumping transcripts. The side_quests field institutionalizes scope discipline at the moment tangents appear.