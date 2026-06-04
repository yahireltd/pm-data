---
id: ADR-011
slug: two-repo-split-code-repo-vs-live-data-repo-at-pm-root-with-p
title: "Two-repo split: code repo vs live data repo at PM_ROOT, with pm-deploy and the server build as the only typecheck"
state: accepted
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets: []
---

## Context
Per ADR-001 the filesystem `.pm/` store is the source of truth, but it was tracked inside the code repo. That conflated two things with opposite lifecycles: code (edited locally, reviewed, pushed) and data (projects/tickets/decisions, mutated continuously and live). Symptoms: every data edit showed up as a code commit, and editing data on the server risked colliding with code pulls. Options: (a) keep one repo and live with the noise/collisions; (b) put data in a database — abandons the filesystem-first, git-diffable model of ADR-001; (c) split data into its own repo.

## Decision
Split into two repos: `yahireltd/pm-tool` (code, read-only on the server) and `yahireltd/pm-data` (the live `.pm/`, written by the server at `PM_ROOT=/opt/pm-tool/data`). `.pm/` is gitignored out of the code repo; a local `.pm/` is only a disposable dev sandbox, never a second source of truth. The server clones both with SEPARATE SSH deploy keys (`id_code` read, `id_data` write — GitHub forbids one key on two repos), via host aliases that bypass org SAML SSO. Code ships via `sudo pm-deploy`, which does `git reset --hard origin/master` (sidestepping bun.lock churn) then `bun install` → `bun run build` → restart, ALWAYS building before restart to avoid stale-chunk client-side exceptions. There is no separate CI typecheck — the production `next build` during deploy is the gate that a change compiles. Data is auto-committed/pushed by a 10-minute timer (and an on-demand admin UI button).

## Consequences
Code pulls never touch data and data edits never collide with code commits; each repo has a clean history and the right access (read vs write). We accept real operational complexity: two repos, two deploy keys, an `outputFileTracingRoot` that must be derived portably so builds work in either checkout, and a deploy that is a hard reset (local server edits are by design impossible — 'never edit on the server'). Using the deploy build as the only typecheck means a type error is caught at deploy time, not before push, so a bad push can fail `pm-deploy` on the server. The live data's durability now rests on the auto-push timer rather than continuous commits.