---
id: T-0314
title: "[security] GitHub read-only token: fine-grained least-privilege + own secret"
type: chore
state: done
created: 2026-06-09T16:10:33Z
updated: 2026-06-22T11:21:05Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - "Replace any classic PAT with a fine-grained PAT scoped to the referenced repos only: Contents:Read (+ implicit Metadata:Read), no write / issues / PR / admin / workflow scopes"
  - Move the token to its own AWS secret instead of piggybacking pmToolAuth.tokenGithubFine, to decouple its blast radius from the Microsoft auth creds
  - Document the minimum required scope; fix the stale code comment in projects.ts (mentions pmToolGithub but actually reads tokenGithubFine)
out_of_scope: []
code_anchors:
  - path: web/app/_actions/projects.ts
    symbol: getGithubToken
  - path: web/app/_actions/projects.ts
    symbol: listRepoBranches
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - security
attention: null
version: 1
---

## Problem

The GitHub token is used by exactly ONE read-only call — listing a repo's branch names to populate the project branch picker (`listRepoBranches`, T-0243). But the token supplied via `GITHUB_TOKEN` / `pmToolAuth.tokenGithubFine` could be an over-broad classic PAT with full `repo` (read+write) scope, grossly exceeding what's used. It also piggybacks the `pmToolAuth` secret, coupling its blast radius to the Microsoft auth creds.

## Least privilege

A fine-grained PAT (or GitHub App installation token) scoped to only the referenced repos, with **Contents: Read** (+ implicit Metadata: Read) and nothing else — no write, issues, PR, admin or workflow scopes. And its own secret, separate from `pmToolAuth`.

## Context

Follow-on to **T-0257**. Files: `web/app/_actions/projects.ts` (`getGithubToken`, `listRepoBranches`). Note the stale comment there referencing a `pmToolGithub` secret that doesn't match the code (it reads `tokenGithubFine` off `pmToolAuth`).

## Conversation

**2026-06-22 11:21 — you**

done

---

**2026-06-22 11:21 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
