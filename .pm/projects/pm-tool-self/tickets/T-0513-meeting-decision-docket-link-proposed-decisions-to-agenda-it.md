---
id: T-0513
title: Meeting decision docket — link proposed decisions to agenda items; nag until every docketed decision gets a disposition
type: feature
state: triaged
created: 2026-07-03T00:30:29Z
updated: 2026-07-03T00:30:29Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - "Agenda items accept an optional decision: ADR-NNN (alongside the existing ticket link); pm_create_meeting/pm_update accept it and pm_get_meeting resolves each linked decision to {id, title, state} the way ticket links resolve to summaries"
  - pm_record_outcome accepts optional decision_id + disposition (accepted | rejected | deferred); accepted/rejected transitions the ADR via its legal state machine and stamps decided_by from the meeting stakeholders; deferred requires a target (a date or a meeting id) and leaves the ADR proposed
  - Marking a meeting held while any docketed decision lacks a recorded disposition sets an attention flag on the meeting (same pattern as ticket attention) and the state-change response lists the undispositioned decisions — soft gate, never a hard block
  - The decisions list (web + pm_list_decisions) shows docket status per proposed decision (docketed for M-NNN / undocketed) and age since proposed; proposed decisions older than 7 days with no docket and no due date are surfaced on the dashboard
  - "AGENTS.md / playbook updated: the workshop pattern is 'docket the decision records onto the agenda', not agenda prose — with the M-008 case as the worked example"
out_of_scope:
  - Hard-blocking the held transition (agents and humans must always be able to record reality)
  - Votes/quorum/approval workflows
  - Calendar (Google/Outlook) integration
  - Auto-scheduling decisions into future meetings
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - meetings
  - decisions
  - governance
attention: null
version: 1
---

## Problem

Meetings can be marked `held` with decisions on the table and nothing recorded — and the tool never notices. Live case (P-0018, M-008 "Segmentation Research", 23 Jun): the body put **4 explicit decisions to Ben**, the meeting went to `held`, the outcomes array stayed **empty for 10 days**, and ADR-007/008/009 sat `proposed` the whole time. Project-wide decision throughput was zero and no surface in the tool showed it. The outcomes were only recorded on 3 Jul by an agent doing a manual reconciliation sweep.

Structural cause: agenda items can link a `ticket` but not a `decision`, so a meeting has no machine-readable docket of what it is supposed to decide — and therefore nothing to check at close-out.

## Context

- Decisions already have a full lifecycle (`proposed → accepted|rejected|superseded`, `rejected → proposed`) and `decided_by`; `pm_update_decision` drives it.
- `pm_record_outcome` already appends outcomes with owner/due and can promote to a ticket — it just doesn't know about decisions.
- Meetings already resolve agenda `ticket` links to summaries, so the resolution pattern exists to copy.
- The ticket `attention` flag + dashboard pattern is the right nag mechanism to reuse.

## Design sketch (MVP)

1. **Docket**: `decision: ADR-NNN` on agenda items; resolved on read like ticket links.
2. **Disposition capture**: `pm_record_outcome(decision_id, disposition)` — `accepted`/`rejected` flips the ADR (legal transitions only) and stamps `decided_by`; `deferred` demands a target date or meeting so a punt is still a recorded, owned event.
3. **Close-out nag**: `held` with undispositioned docket → meeting gets an attention flag listing them; dashboard shows it exactly like shipped-but-unfinalized tickets.
4. **Reverse view**: each proposed decision shows where it is docketed; undocketed + aging = the "decision rotting" signal on the dashboard.

## Why this matters

The P-0018 evaluation (3 Jul) found decision throughput to be the single binding constraint on the whole programme — build was weeks ahead of ratification. This feature makes that failure mode visible at the moment it happens (meeting close) instead of weeks later in an audit.

**Note on Definition of Ready**: code_anchors intentionally left empty — needs the pm-tool maintainer to anchor the meeting schema, outcome handler, and dashboard views before this is claimable.