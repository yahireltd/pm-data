---
id: T-0395
title: "Docs-drift gate: fail the build when schema/types/tools change without a docs update (§8 enforcement)"
type: feature
state: done
created: 2026-06-16T19:53:44Z
updated: 2026-06-22T18:26:27Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] A change that edits a schema/type/tool (schemas/, cli/src/types.ts, mcp-server/src/tools/) without a matching doc edit (SCHEMA.md / web/app/docs/content.ts / web/app/help/content/) is flagged by the lint/build"
  - "[x] An explicit escape hatch (e.g. a docs-ok: <reason> marker) lets a genuine no-docs-needed change through, recording the reason"
  - "[x] Wired into the deploy build (the existing type-gate) so it actually blocks; warn-first then promote to hard fail"
out_of_scope: []
code_anchors:
  - path: linter/src/docs-drift.ts
    symbol: checkDocsDrift (new source-diff check)
  - path: linter/bin/pm-docs-drift
    symbol: runner bin (new)
  - path: docs/development.md
    symbol: pm-deploy docs-drift step
  - path: AGENTS.md
    symbol: §8 docs-travel-with-change enforcement
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260622-1810
    model: claude-opus-4-8
    started: 2026-06-22T18:10:38Z
    status: completed
    ended: 2026-06-22T18:15:07Z
    summary: |-
      **What we did:** Added an automatic check that runs on every deploy and flags when someone changes the core data definitions or the assistant's tools but forgets to update the matching documentation. For now it's a gentle warning — it points out the gap in the deploy log without stopping the deploy — and there's an explicit way to say "no docs needed here, and here's why."

      **Why:** We have a written rule that documentation should travel with the change, but it was only a norm — easy to forget under load. A whole feature session once shipped with the data-model doc, the developer wiki, and the user help all left out of date. Rules that rely on remembering eventually fail.

      **If we did nothing:** Documentation would keep silently drifting out of sync with the code, so the next person — or assistant — reading the docs would be misled, and the gap would only surface much later when something didn't match.

      **The benefit:** The deploy now points out documentation that's fallen behind the moment it happens, so it gets fixed while it's fresh — turning a good-intentions rule into something the system actually checks.
    test_plan: |-
      Verified before handoff against real commit history:
      1. A deploy range that changed a tool AND its doc (T-0267's range) → passes ("travels with a docs edit").
      2. A commit that changed a tool file with no matching doc (T-0265's record-outcome fix) → flagged as drift (warning).
      3. The same with --strict → exits non-zero (the future hard-block mode).
      4. A docs-only / no-code range → passes.
      5. The 'docs-ok: <reason>' escape-hatch regex is recognised and captures the reason.

      Reviewer:
      - Run `bun linter/bin/pm-docs-drift --range <a..b>` on any range to see it; add `--strict` to make it block.
      - After deploy, the deploy log shows a "docs-drift" line (warn-only) for the just-pulled range.
      - DEPLOY NOTE (the not-in-repo half, like T-0265): at deploy time the real `/usr/local/bin/pm-deploy` gains a warn-mode docs-drift step — capture the pre-pull SHA, then `bun linter/bin/pm-docs-drift --range OLD..NEW || true` so it never blocks while in warn mode.

      Scope: warn-first by design; promoting to a hard block (drop the `|| true`, pass `--strict`) is a deliberate later step once the team's used to it. v1 matching is coarse — any of the three doc surfaces satisfies any code change; tightening per-surface mapping can come later.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: AGENTS.md §8 (the docs-travel-with-change norm) now documents the enforcing gate, and docs/development.md describes the warn-mode docs-drift step in pm-deploy.
labels:
  - forcing-function
  - docs
  - dx
attention: null
version: 16
collaborators:
  - kind: human
    name: Austin Pickering
branch: t0395-docs-drift-gate
---

## Problem
AGENTS.md §8 ("docs travel WITH the change") is a norm with no teeth, so it gets forgotten under load — a whole feature session shipped with SCHEMA.md, the `/docs` dev wiki, and the `/help` user guide all left stale. Same root cause as the close-the-loop gap: rules that depend on the agent remembering fail. See ADR (this project).

## Proposal
Add a docs-drift check to the lint/deploy build:
- If a commit/diff touches `schemas/`, `cli/src/types.ts`, or `mcp-server/src/tools/` AND does NOT touch `SCHEMA.md` / `web/app/docs/content.ts` / `web/app/help/content/`, flag it.
- Warn first (a few weeks), then promote to a hard fail so stale docs can't deploy.
- Provide an explicit escape hatch — e.g. a `docs-ok: <reason>` marker in the commit/PR — that records WHY no doc was needed (a scar, like force-anyway).
- Optional companion: a Claude Code stop/commit hook running the same check earlier.

## Acceptance criteria
- A change that edits a schema/type/tool without a matching doc edit is flagged by the lint/build.
- An explicit escape hatch lets a genuine no-docs-needed change through, recording the reason.
- Wired into the deploy build (the existing type-gate) so it actually blocks.

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep check, re-confirmed) — unbuilt; build THIRD (after T-0265 + T-0192).**

§8 "docs travel with the change" is still pure prose, no gate anywhere. **Scope correction:** the existing pm-lint linter is the *wrong* host — it only lints `.pm/` data markdown, so this needs a **new source-diff check**, not a new lint rule. Sequence it after **T-0265** (which gives it a real pre-deploy gate to hook into) and reuse **T-0192's** commit-hook + git-diff plumbing rather than rebuilding. Note: the agent-run `docs_note` field is self-reported, not an enforced gate — don't mistake it for partial work.

**2026-06-22 18:15 claude:** Run run-20260622-1810 completed — **What we did:** Added an automatic check that runs on every deploy and flags when someone changes the core data definitions or the assistant's tools but forgets to update the matching documentation. For now it's a gentle warning — it points out the gap in the deploy log without stopping the deploy — and there's an explicit way to say "no docs needed here, and here's why."

**Why:** We have a written rule that documentation should travel with the change, but it was only a norm — easy to forget under load. A whole feature session once shipped with the data-model doc, the developer wiki, and the user help all left out of date. Rules that rely on remembering eventually fail.

**If we did nothing:** Documentation would keep silently drifting out of sync with the code, so the next person — or assistant — reading the docs would be misled, and the gap would only surface much later when something didn't match.

**The benefit:** The deploy now points out documentation that's fallen behind the moment it happens, so it gets fixed while it's fresh — turning a good-intentions rule into something the system actually checks.

**2026-06-22 18:25 claude-code:** **Post-deploy fix 2026-06-22 (commit `ff1be4e`) — the gate's first live run caught a bug in itself.**

On the T-0395 deploy, the docs-drift step printed "skipped — docs-ok…" even though nothing needed skipping. The cause: the `docs-ok:` escape-hatch regex matched the marker *anywhere* in a commit message — and this ticket's own commit message *describes* the docs-ok feature, so it was wrongly read as opted-out. A real tool change whose message merely mentioned `docs-ok:` would have been silently let through.

Fix: the marker must now sit on its **own line** (a deliberate trailer), so prose that merely mentions "docs-ok:" can't trip it. Also reordered the reporting so the printed message always matches the decision (no-code → OK; docs-travelled → OK; trailer → skipped; else → drift).

Re-verified live: the T-0395 range now reports "OK — no schema/type/tool changes"; a real tool-change-without-docs commit still warns; a genuine `docs-ok:` trailer is still recognised. Deployed and live.

---

**2026-06-22 18:26 — you**

Check shows up doc drift
