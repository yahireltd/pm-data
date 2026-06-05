---
id: TS-007
slug: pre-project-parity-hard-delete-plain-language-notes-build-an
title: Pre-project parity + hard-delete + plain-language notes — build and adversarial review
project: pm-tool-self
created: 2026-06-05T15:39:34Z
updated: 2026-06-05T15:40:25Z
decisions:
  - "Shipped three things together: pre-project meetings as a first-class feature that moves to the real project on convert (ADR-029); a true delete for junk tickets, admin-only with confirmation (ADR-030); and a rule that agents write notes in plain, non-technical language (T-0231)."
  - "Learning: the independent review pass we ran before shipping caught a command-line syntax error that the normal server build could never have flagged — because the build only checks the web app, not the command-line or agent-tool code. Of 19 raw issues raised, 9 were genuine after a second checking pass weeded out 10 false alarms. Takeaway: changes to the command-line / agent-tool code need their own review, since nothing automatically type-checks them."
actions: []
side_quests:
  - There is no way to write a project status note through the agent tools — only the web/command-line have it. Worth adding a tool so agents can keep the heartbeat going.
  - The agent tools don't validate notification settings the way the web and command-line do (a pre-existing gap, now also reachable for pre-project people). Low priority.
  - Hard-delete exists for tickets only — the same "remove the junk" need will come up for other throwaway items like test sprints. Future follow-up.
problem: "Three pieces of work in one session: (1) pre-projects lagged real projects — their people were names-only with no email, and they couldn't hold meetings, so kickoff/scoping couldn't be planned; (2) there was no way to delete a junk ticket; (3) agent-written notes were too technical for stakeholders. All shipped with no local typecheck (the server build only compiles the web app, not the command-line or agent-tool code)."
success_criterion: All three live in production; an independent review pass run before shipping; every confirmed issue fixed; the work recorded against its tickets.
phase: build
---

# TS-007: Pre-project parity + hard-delete + plain-language notes — build and adversarial review

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
