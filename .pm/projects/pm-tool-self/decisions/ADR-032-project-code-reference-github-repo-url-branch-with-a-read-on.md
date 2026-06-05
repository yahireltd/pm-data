---
id: ADR-032
slug: project-code-reference-github-repo-url-branch-with-a-read-on
title: Project code reference = GitHub repo URL + branch, with a read-only token
state: accepted
decided: 2026-06-05
decided_by:
  - Austin
  - claude
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0243
---

## Context

A project's code path (`code_root`) was a local filesystem path, used only to verify ticket code-anchors exist on disk. We wanted it to be crystal-clear which **GitHub repo + branch** a project (and the new system projects) works on, with the branch picked from a live list, and code-anchors that link out to GitHub.

## Decision

Add `repo_url` (GitHub URL) + `branch` to a project — the canonical "which repo/branch are we on". `code_root` is kept as a legacy field for projects that rely on a local checkout. A **read-only GitHub PAT** (server env `GITHUB_TOKEN`) powers a live branch **dropdown** via the `listRepoBranches` server action, with a graceful **free-text fallback** when no token is configured. Ticket code-anchors link to GitHub blob URLs (at the project's branch, with line anchors) when `repo_url` is set; the linter **skips the on-disk anchor check** for those projects (no local checkout to stat).

## Consequences

- The branch dropdown needs a **fine-grained read-only token** (Contents + Metadata: read on the org's repos) set as `GITHUB_TOKEN` on the server (or wired to AWS Secrets later, mirroring `pmToolAuth`). Until it's set, the branch is a free-text field — everything else still works.
- `code_root` remains for legacy/local-checkout projects; their on-disk anchor checks are unchanged.
- **Read-only only** — no writes to GitHub (no PR/commit integration).
- New fields are additive + optional, so existing projects round-trip unchanged.
- Anchor paths are no longer verified to exist for `repo_url` projects (the link is built but not checked against the remote) — an acceptable trade for clickable cross-repo links.