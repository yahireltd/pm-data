---
id: TS-008
slug: spr-025-fixes-found-in-usage-testing
title: "SPR-025: fixes found in usage / testing"
project: pm-tool-self
created: 2026-06-05T21:28:05Z
updated: 2026-06-05T21:28:15Z
decisions:
  - Tickets AND meetings can be permanently hard-deleted (admin-only, confirm-guarded), distinct from soft-dispose wontfix/duplicate — ADR-030 (T-0226, T-0239).
  - The human close dialog pre-fills from the agent's records attestation, so closing a reviewed ticket is one confirm, not a re-entry (T-0236).
  - Agent-written human-facing notes must be plain language (what we did / why / cost of doing nothing / benefit) — added as AGENTS.md §11 and reinforced in the MCP tool descriptions (T-0231).
  - Charter + pre-project free-text framing fields became WYSIWYG by storing the markdown as a single-element string[], so the phase gates, convert carry-over, schema and linter were left untouched (T-0235).
  - Phase revert is a deliberate, gate-free backward move that loses no later-stage data, reachable from the viewed phase (T-0227).
actions: []
side_quests: []
problem: "Dogfooding pm-tool surfaced a batch of UX + correctness gaps: no way to delete tickets/meetings, un-clickable list titles, fiddly rich-text focus, an inbox polluted by calendar mail, close-the-loop friction, jargon in agent notes, plain-textarea framing fields, no way to revert a phase, small fonts/flat icons, and missing notification counts."
success_criterion: Each gap is shipped + recorded, the board reflects reality, and the tool is calmer and clearer to use day to day.
phase: test
---

# TS-008: SPR-025: fixes found in usage / testing

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
