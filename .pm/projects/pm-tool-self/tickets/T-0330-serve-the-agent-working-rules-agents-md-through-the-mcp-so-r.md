---
id: T-0330
title: Serve the agent working rules (AGENTS.md) through the MCP so remote agents don't need the code checkout
type: feature
state: done
created: 2026-06-09T19:28:25Z
updated: 2026-06-22T17:00:33Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] An agent with ONLY the remote MCP connection (no code checkout) can retrieve the full working rules before touching any ticket"
  - "[x] The served conventions and the repo AGENTS.md come from a single source of truth (no second copy to drift)"
  - "[x] The dev wiki section on the MCP tool surface documents how conventions are served"
  - "[x] Bonus: pm_claim_ticket nudges agents who have not fetched the conventions (only if cheap to implement)"
out_of_scope: []
code_anchors:
  - path: mcp-server/src/server.ts
    symbol: loadAgentsConventions + instructions option + pm://conventions resource
  - path: web/app/docs/content.ts
    symbol: MCP tool surface (Resources) + Conventions sections
  - path: AGENTS.md
    symbol: the served source (unchanged)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260622-1645
    model: claude-opus-4-8
    started: 2026-06-22T16:45:26Z
    status: completed
    ended: 2026-06-22T16:50:11Z
    summary: |-
      **What we did:** We made the project-management tool hand its own working rules to any AI assistant that connects to it — even one running on a different computer with no copy of our code. The rules now arrive automatically the moment an assistant connects, and can also be pulled up on demand.

      **Why:** Those rules previously lived only inside our code. An assistant connecting to the live tool from somewhere else had no way to read them, so it would use the tool while blind to the conventions our safety checks already assume it knows.

      **If we did nothing:** Assistants working from other machines would keep missing steps the tool quietly expects — claiming work before starting, recording decisions, closing the loop — simply because they couldn't see the rulebook. That means untracked work and broken conventions, the exact problem this tool exists to prevent.

      **The benefit:** Every connecting assistant now starts already knowing how we work, from a single copy of the rules that can't fall out of sync with a duplicate — and our developer guide explains how it's delivered.
    test_plan: |-
      Reviewer checklist (the change needs an MCP server **restart** to take effect — a web-only deploy won't pick it up, as the rules are read once at startup):

      1. Restart the MCP server, then reconnect a fresh MCP client.
      2. Confirm the server's `instructions` now contain the AGENTS.md working rules (claim-before-code, Definition of Ready, close-the-loop, plain-language notes, agent policy + effective branch).
      3. Fetch the `pm://conventions` resource — confirm it returns the full AGENTS.md markdown.
      4. Single-source check: edit one line in AGENTS.md, restart the MCP, re-check — the edit appears in BOTH the `instructions` and the `pm://conventions` resource (proves there's no second copy).
      5. Graceful fallback: if AGENTS.md were missing, the server still starts — `instructions` is simply omitted and the resource returns a clear "not available" error (no crash).
      6. Web: open /docs → "MCP tool surface" → Resources, and the Conventions section — both now mention `pm://conventions` + the `instructions` field.

      Cross-impact / notes for the reviewer:
      - Files: `mcp-server/src/server.ts` (loadAgentsConventions + instructions option + pm://conventions resource), `web/app/docs/content.ts`.
      - AC4 (claim-time nudge) is intentionally satisfied via the instructions mechanism — every client already receives the rules on connect — rather than a separate per-agent tracking hook, because the HTTP server is stateless per request so true "has this agent fetched" tracking isn't cheap.
      - `mcp-server` typechecks clean for this change; the one pre-existing unrelated error (`record-outcome.ts:84`) is NOT introduced here and is tracked under T-0265.
      - To ship: push branch `t0330-serve-agents-md-mcp`, merge, then **restart the MCP** (not just a web deploy).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Dev wiki (/docs MCP-surface Resources + Conventions sections) updated to document that AGENTS.md is now served via the MCP instructions field and the pm://conventions resource.
labels:
  - mcp
  - dogfood-find
  - agent-experience
attention: null
version: 16
collaborators:
  - kind: human
    name: austin
  - kind: human
    name: Austin Pickering
branch: t0330-serve-agents-md-mcp
---

## Problem

The working rules for agents (AGENTS.md — claim before you code, Definition of Ready, close-the-loop attestation, plain-language write-ups, agent policy) live only in the code repository. An agent connecting to the live MCP from a machine WITHOUT the pm-tool checkout has no way to read them — it will use the tools while blind to the conventions the force-gates assume it knows. This came up in practice today: an agent planning a sprint for a different project (yasystem) only followed the conventions because the pm-tool repo happened to be on the same machine.

## Ideas

Pick whichever fits the architecture best:

- Include the conventions (or a distilled version) in the MCP server's instructions field so every connecting client receives them automatically.
- Expose them as an MCP resource and/or a pm_get_conventions tool.
- Return a short conventions digest inside pm_get_project (which agents already call first), alongside the agent_policy it already returns.

The conventions text should be served from one source of truth so the repo copy and the served copy cannot drift.

## Conversation

**2026-06-22 16:16 claude-code:** **Verified 2026-06-22 (deep check, re-confirmed) — genuinely unbuilt; small, self-contained.**

The MCP server serves nothing from AGENTS.md today: no `instructions` field, no conventions tool or resource, and AGENTS.md is never read by any code path (the only "conventions" text anywhere is a hand-typed mirror in the web docs page — which is itself the drift this ticket warns about). Cleanest build: read AGENTS.md **once on disk** at server startup and expose it two ways from that single read — the MCP `instructions` field (every client gets it automatically) **+** a `pm://conventions` resource. Heads-up: the claim-time "nudge" bonus (AC4) is hard because the server is stateless per request — keep it a static one-line pointer or skip. And the MCP server needs a **restart** to pick this up (a deploy won't hot-load it). No dependency on the other four — orderable anywhere.

**2026-06-22 16:50 claude:** Run run-20260622-1645 completed — **What we did:** We made the project-management tool hand its own working rules to any AI assistant that connects to it — even one running on a different computer with no copy of our code. The rules now arrive automatically the moment an assistant connects, and can also be pulled up on demand.

**Why:** Those rules previously lived only inside our code. An assistant connecting to the live tool from somewhere else had no way to read them, so it would use the tool while blind to the conventions our safety checks already assume it knows.

**If we did nothing:** Assistants working from other machines would keep missing steps the tool quietly expects — claiming work before starting, recording decisions, closing the loop — simply because they couldn't see the rulebook. That means untracked work and broken conventions, the exact problem this tool exists to prevent.

**The benefit:** Every connecting assistant now starts already knowing how we work, from a single copy of the rules that can't fall out of sync with a duplicate — and our developer guide explains how it's delivered.

---

**2026-06-22 17:00 — you**

done and live
