---
id: T-0252
title: 'Tighten testing: agent hands the human a detailed "what to test" checklist at review'
type: feature
state: review
priority: p2
created: 2026-06-05T21:38:52Z
updated: 2026-06-08T18:28:39Z
project: pm-tool-self
section: null
parent: null
children: []
order: 80896
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - At review, the agent's hand-off includes a SPECIFIC, thorough 'what to test' checklist for the human — the exact things to click/try, the happy path, AND the edge cases — not a vague note.
  - "The checklist calls out cross-impact explicitly: if a shared function/component/model was changed, it names the other places that use it for the human to re-check."
  - AGENTS.md sets this as the required standard for a run's test_plan, so it's an expectation every run meets, not left to chance.
  - Where automated test coverage around the changed area is thin, the agent adds/extends tests as part of the work.
out_of_scope:
  - A CI / pre-deploy automated-test gate (decided out — 'just write more tests', no blocking gate).
  - Automated cross-impact / caller-analysis tooling — cross-impact is handled by the agent naming affected callers in the test checklist, not by a tool.
  - Pre-work risk identification (tracked separately near T-0269) and the review-UI surfacing of the checklist (that's T-0268).
code_anchors:
  - path: AGENTS.md
    symbol: §11 (run test_plan standard)
  - path: mcp-server/src/tools/complete-run.ts
    symbol: test_plan
  - path: web/app/_components/ReviewBanner.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2159
    model: claude
    started: 2026-06-05T21:59:16Z
    status: abandoned
    summary: "Reconciling an orphaned claim. This ticket was claimed via the web UI on 5 June but never actually worked — no progress was recorded and no code was written — yet it sat in \"in progress\" ever since, wrongly suggesting work was underway. Returning it to the backlog. The underlying need (tighten our testing rigour: automated checks, edge-case evaluation, cross-impact checks when a shared function changes, and a solid list of things for the human to test) is still wanted — but it's too broad as written and should be broken into concrete, testable pieces before an agent picks it up again."
    ended: 2026-06-08T18:04:47Z
  - id: run-20260608-1826
    model: claude-opus-4-8
    started: 2026-06-08T18:26:38Z
    status: completed
    ended: 2026-06-08T18:28:39Z
    summary: "What we did: set a clear standard that, when an agent finishes a piece of work, the \"how to test this\" hand-off it gives you must be a proper, specific checklist — the exact things to click and try, the normal path AND the edge cases, and (importantly) a callout of any knock-on areas: if the agent changed something shared, it now has to name the other screens that use it so you re-check those too. We also told the agent tool to ask for that same standard at the point the agent writes it, and to top up automated tests where they're thin around a change. Why: we move fast, and the weakest link is verification — a vague \"looks fine, test it\" leaves you guessing what to check, and regressions slip through in places nobody obviously touched. What would have happened if we did nothing: review would stay a guessing game and the \"I changed a shared function and a different screen quietly broke\" class of bug would keep getting through. The benefit: every review now comes with a do-this list you can follow top-to-bottom, so you can sign off with real confidence and catch knock-on breakage before it ships."
    test_plan: |-
      This change is a written standard (agent guidance), not a UI feature — so "testing" it is confirming the wording sets the right bar. Follow top-to-bottom:

      1. **Read AGENTS.md §1** → the new bullet "The `test_plan` is the human's review checklist — make it specific (T-0252)". Confirm it clearly requires: a concrete do-this checklist (not a vague note), happy path AND edge cases, cross-impact (name affected callers/screens when shared code changes), and extending tests where coverage is thin. Check it reads as a standard you'd want every ticket held to.
      2. **Read the tool field** at `mcp-server/src/tools/complete-run.ts:76-81` → the `test_plan` description now states the same standard, so an agent sees it at the moment it writes the plan. Confirm it matches §1 (no drift between the two).
      3. **Edge case — consistency:** AGENTS.md §11 also mentions `test_plan` (keep-technical-detail-here). Confirm the two mentions don't contradict (§11 = where detail goes; §1 = what the detail must contain). They shouldn't.
      4. **Cross-impact:** the only thing that consumes the tool-field text is the MCP tool schema agents read — so the `complete-run.ts` edit needs the **mcp-server redeployed** before agents see the new wording (the AGENTS.md half is guidance and takes effect as soon as it's read). No code behaviour changed — the tool still accepts `test_plan` exactly as before; this is description text only, so there's nothing functional to break.
      5. **Meta-check:** this very review hand-off is written to the new standard — if *this* checklist is specific and followable, the standard is doing its job. If the bar reads wrong, send it back with what you'd change.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: AGENTS.md §1 gains the test_plan-as-review-checklist standard; mirrored in the pm_complete_run test_plan field description (mcp-server/src/tools/complete-run.ts).
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-08T18:28:39Z
---

## Problem

We move fast, and the weak point is verification: an agent can finish work and hand over a thin or vague "test plan", leaving the human unsure what to actually check — so regressions slip through, especially when a shared function/component changed and a *different* screen breaks.

## What we want

The fix is about the **agent stipulating, in detail, what the human needs to test for the review** — a real checklist, not a sentence:
- The exact things to click/try, the happy path, and the **edge cases**.
- **Cross-impact spelled out**: "I changed shared function X — also re-check screens Y and Z that call it." The agent does the impact thinking and hands the human the list (no separate tooling).
- Backed by an **AGENTS.md standard** so every run's `test_plan` meets this bar.
- Plus **more automated tests** where coverage around the change is thin (no CI gate — that was explicitly out).

## Relates / not-duplicates

- **T-0268** (richer review handoff) is the *build* side — surfacing this checklist nicely inline at review. This ticket is the *content/discipline* side — the agent producing a thorough checklist in the first place.
- The pre-work "identify risk before starting" idea lives near **T-0269** (risk/rigor dial), kept out of scope here.

## Origin

Re-scoped from the original broad "tighten up testing" (per Austin, 2026-06-08) — narrowed to the detailed test-checklist-at-review intent; automated tooling / CI gate / cross-impact tooling explicitly dropped.

## Conversation

**2026-06-08 18:28 claude:** Run run-20260608-1826 completed — What we did: set a clear standard that, when an agent finishes a piece of work, the "how to test this" hand-off it gives you must be a proper, specific checklist — the exact things to click and try, the normal path AND the edge cases, and (importantly) a callout of any knock-on areas: if the agent changed something shared, it now has to name the other screens that use it so you re-check those too. We also told the agent tool to ask for that same standard at the point the agent writes it, and to top up automated tests where they're thin around a change. Why: we move fast, and the weakest link is verification — a vague "looks fine, test it" leaves you guessing what to check, and regressions slip through in places nobody obviously touched. What would have happened if we did nothing: review would stay a guessing game and the "I changed a shared function and a different screen quietly broke" class of bug would keep getting through. The benefit: every review now comes with a do-this list you can follow top-to-bottom, so you can sign off with real confidence and catch knock-on breakage before it ships.
