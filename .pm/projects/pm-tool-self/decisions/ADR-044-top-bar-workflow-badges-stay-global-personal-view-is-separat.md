---
id: ADR-044
slug: top-bar-workflow-badges-stay-global-personal-view-is-separat
title: Top-bar workflow badges stay global; personal view is separate
state: accepted
decided: 2026-06-18
decided_by:
  - Austin
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0421
  - T-0420
version: 1
---

## Context

T-0421 asked whether the admin top-bar badges (Tickets · Inbox · Review · Ready · In progress) should count globally or per the signed-in user ("assigned to me"). They've always counted globally, and the question ties to T-0420 (a real personal "My work" view).

## Decision

The top-bar badges **stay global** — they're the admin's at-a-glance workflow-health overview ("how much is in review / ready / in flight across everything"). The **personal** angle ("what's assigned to me / unassigned") is delivered separately by T-0420 (a reworked `/me` plus an assignee filter on `/tickets`), rather than by overloading the global badges with a personal meaning.

## Consequences

- One clear meaning per badge; tooltips added so each badge matches the page it opens (T-0421). The Review badge now mirrors the Review page exactly; Tickets shows no count (its page is "all tickets").
- Personal "my work" lives in its own surface (T-0420), so global and personal don't fight over the same widget.
- If a per-user badge is wanted later, it can be added alongside the global one rather than replacing it.