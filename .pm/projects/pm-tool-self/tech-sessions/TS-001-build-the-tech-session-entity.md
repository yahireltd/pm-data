---
id: TS-001
slug: build-the-tech-session-entity
title: Build the tech-session entity
project: pm-tool-self
created: 2026-06-02T16:20:46Z
updated: 2026-06-02T16:27:19Z
decisions:
  - Mirror the Sprint entity wiring end-to-end (schema/types/ids/paths/loaders/CLI/linter/MCP/web)
  - Give it MCP tools (create/list/get/record) so agents capture + recall context — the agent-context purpose
actions:
  - text: Mark T-0156 done and complete SPR-005
    ticket: T-0156
side_quests:
  - A 'promote side-quest -> backlog ticket' button would close the loop (future)
problem: "Course rock #27: capture working/deep-dive sessions as decisions+actions, not transcripts — and give agents a place to record/recall context across sessions"
success_criterion: TS entity across schema/CLI/MCP/web + linter validation; agents can create and read tech-sessions via MCP
phase: build
---

# TS-001: Build the tech-session entity

## Problem

_Course rock #27: capture working/deep-dive sessions as decisions+actions, not transcripts — and give agents a place to record/recall context across sessions_

## Success criterion

_TS entity across schema/CLI/MCP/web + linter validation; agents can create and read tech-sessions via MCP_

## Decisions

_Capture decisions, not a transcript._

## Actions

_Next actions — link follow-up tickets._

## Side-quests

_Tangents surfaced — route to the backlog, don't absorb._
