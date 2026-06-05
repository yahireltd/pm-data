---
id: TS-011
slug: github-repo-reference-per-project-agent-guardrails
title: GitHub repo reference + per-project agent guardrails
project: pm-tool-self
created: 2026-06-05T21:29:06Z
updated: 2026-06-05T21:29:23Z
decisions:
  - Project code reference = GitHub repo URL + branch; a read-only token (stored as tokenGithubFine on the pmToolAuth AWS secret) powers a live branch dropdown; ticket code anchors link to GitHub at that branch — ADR-032 (T-0243).
  - Per-project agent_policy (allow_commit / allow_push; unset = permissive) is surfaced on get-project/get-ticket and enforced by a hard claim-time gate (acknowledge_policy); since pm-tool is not in the git path, GitHub branch protection is the hard stop for production branches — ADR-033 (T-0247).
actions:
  - text: Run the security review / hardening pass (admin + GitHub token + MCP auth)
    ticket: T-0249
  - text: Lock production projects (e.g. yasystem main) + add GitHub branch protection
    ticket: T-0247
side_quests: []
problem: It wasn't crystal-clear which repo/branch a project works on, and nothing constrained an agent to the right branch or limited commit/push on production code.
success_criterion: A project states its GitHub repo + branch (with a live read-only branch list); code anchors link to GitHub; per-project commit/push guardrails are surfaced to agents and gated at claim.
phase: test
---

# TS-011: GitHub repo reference + per-project agent guardrails

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
