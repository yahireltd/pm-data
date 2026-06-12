---
id: T-0352
title: Branch lives on the ticket — pin at scoping, or agent cuts a ticket branch (ADR-039)
type: feature
state: done
created: 2026-06-10T15:00:28Z
updated: 2026-06-12T01:44:42Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Ticket schema/types carry optional branch; editable in web ticket detail and via pm_update_ticket"
  - "[x] pm_claim_ticket returns the effective branch (ticket ?? surface ?? project) and the policy together"
  - "[x] Convention documented in AGENTS.md: unset branch + protected default → agent cuts t<NNNN>-<slug> branch and records it on the ticket"
  - "[x] Sub-tasks inherit the parent ticket's branch when their own is unset"
  - "[x] Yasystem project can drop 'Main Branch' from its name with no information lost (branch is data, not naming)"
out_of_scope:
  - Per-branch agent policy (deferred, ADR-039)
  - Automated branch cleanup/merge detection
code_anchors:
  - path: schemas/ticket.schema.json
  - path: cli/src/types.ts
    symbol: Ticket
  - path: mcp-server/src/tools/claim-ticket.ts
    symbol: claim payload — add effective branch resolution
  - path: mcp-server/src/tools/update-ticket.ts
  - path: web/app/tickets/[id]/page.tsx
    symbol: meta column
  - path: AGENTS.md
    symbol: §1 — add branch convention
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260611-2304
    model: claude-fable-5
    started: 2026-06-11T23:04:27Z
    status: completed
    progress:
      - at: 2026-06-11T23:10:03Z
        note: "Implementation complete: every ticket can now carry its own working branch, so several streams of work live inside ONE project instead of a project per branch. When the AI picks up a ticket it's told the one branch to work on — the ticket's own pin, else the parent ticket's (sub-tasks inherit), else the area's, else the project default — and on protected projects the rule is now written down: cut a ticket-named branch and record it back before committing, so the stream survives the branch's eventual merge. Branch is editable on the ticket page (with a hint showing what it inherits) and shows as a small tag on lists. Nine-scenario test suite passes; both typechecks clean. Adversarial review running before commit."
    ended: 2026-06-11T23:31:13Z
    summary: |-
      **What we did**
      Every ticket can now carry its own working branch. When the AI picks up a ticket it's told the one branch to work on, resolved through a single chain: the ticket's own pin, else its parent ticket's (sub-tasks inherit), else the area's, else the project's default. The branch is editable on the ticket page (a grey hint shows what it would inherit), shows as a small tag on lists, and on protected projects the working rule is now written down: no pinned branch means the AI cuts a ticket-named branch and records it back before saving anything.

      **Why we did it**
      Several streams of work were forcing wrong shapes: a whole project got named "Yasystem Main Branch" because a branch had nowhere else to live, and the Logistics rollout became a separate project just because it's a branch of the ERP. A project per branch loses its history the moment the branch merges and is deleted.

      **If we did nothing**
      Projects would keep multiplying per branch, the names would keep lying ("Main Branch" as identity), and finished branches would keep orphaning the record of what was built on them.

      **The benefit**
      One Yasystem can hold the refund stream, the picking-dashboard stream, and master work side by side — each ticket pointing at its branch — and the record survives every merge. This was the last building block: the tidy-up (one Websites system, Logistics folded into Yasystem, the rename) can now go ahead.
    test_plan: |-
      After `pm-deploy` (commit 0c522a9 — restarts the MCP so claim/get/update pick up branch behaviour; this deploy also carries T-0351 surfaces if not deployed yet) and a hard refresh:

      **Web**
      1. Open any project ticket → right column has a new **Branch** field. Empty shows a grey hint like "inherits master (project)" — on a ticket tagged with a surface that has a branch override, the hint shows that instead, with "(surface)".
      2. Click it, type a branch name, Enter → saves; the ticket list now shows a small branch tag on that row. Clear the field → back to the inherits hint.
      3. Two-tab conflict: edit the branch in one tab, then in a second (unrefreshed) tab — second save rejected with the "changed by someone else" note, text kept.

      **MCP (agents)**
      4. `pm_get_ticket` on any project ticket → response has `branch: {branch, source}` where source is one of ticket/parent/surface/project, plus `agent_policy` as before.
      5. `pm_claim_ticket` → returns `branch` AND `agent_policy` together (even on unlocked projects — review fix).
      6. On the read-only "Websites (yasite)" system: claiming without `acknowledge_policy` is refused, and the refusal names the effective branch — if it's just the protected default, the message tells the agent to cut a `t<NNNN>-<slug>` branch and record it.
      7. `pm_update_ticket` with `branch: "t9999-test"` sets it; `branch: ""` clears it.

      **Edge cases (review catches — worth confirming)**
      8. A sub-task (ticket with a parent) and no own branch inherits the parent's pin (`source: "parent"`); if the parent lives in a DIFFERENT project, it does NOT inherit (falls back to surface/project) — branch names are repo-scoped.
      9. An inbox ticket with a pinned branch still reports it (`source: "ticket"`).

      **Cross-impact**
      10. Moving a ticket between projects keeps its pinned branch (deliberate — that's how Logistics tickets carry their stream into Yasystem) but clears its surface tag (T-0351 behaviour).
      11. /docs conventions item 12 + the MCP table, /help Tickets / Dev Tickets / ticket pages describe the new field.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: AGENTS.md §1+§12 (the convention), SCHEMA.md, dev wiki (conventions, tool rows, ticket yaml), three help screens — same commit. ADR-039's open point (pinned branch vs locked project policy) settled as pinned-wins and recorded in AGENTS.md §12.
labels:
  - taxonomy
attention: null
version: 15
---

## Problem

The ERP (Ya-Hire-Management) is one repo with several concurrent feature branches (master, pickingsalesdashfriday, refund-hardeningt0331), but branch is a single project-level value — so the branch ended up in the project NAME ("Yasystem Main Branch"), and there's no way to tell an agent "this ticket is the refund-hardening stream". A project per branch is wrong: branches die at merge and would orphan their ticket history.

## Design (ADR-039, "both" model)

- Ticket gains optional `branch`. A human pins it at scoping (web edit + pm_update_ticket); children/sub-tasks inherit from their parent ticket when unset.
- Effective branch at claim = ticket.branch ?? surface.branch ?? project.branch. `pm_claim_ticket` returns it explicitly.
- If unset AND the project default is protected (agent_policy disallows commit/push), the agent's convention is to cut a ticket-named branch (e.g. `t0351-branch-on-ticket`) and record it back on the ticket — so the stream survives the branch's deletion and the run history says where the code went. Document the convention in AGENTS.md §1.
- Web: branch shown/editable on the ticket detail meta column; ticket rows in the same project can show the branch chip when it differs from the default.
- Agent policy stays project-wide (per-branch policy deferred).

## Why it matters

Work stops being lost when a branch ends: the tickets, runs, and the recorded branch name stay on the long-lived project. Agents stop guessing which branch a stream belongs to.

## Conversation

**2026-06-11 23:31 claude-code:** Run run-20260611-2304 completed — **What we did**
Every ticket can now carry its own working branch. When the AI picks up a ticket it's told the one branch to work on, resolved through a single chain: the ticket's own pin, else its parent ticket's (sub-tasks inherit), else the area's, else the project's default. The branch is editable on the ticket page (a grey hint shows what it would inherit), shows as a small tag on lists, and on protected projects the working rule is now written down: no pinned branch means the AI cuts a ticket-named branch and records it back before saving anything.

**Why we did it**
Several streams of work were forcing wrong shapes: a whole project got named "Yasystem Main Branch" because a branch had nowhere else to live, and the Logistics rollout became a separate project just because it's a branch of the ERP. A project per branch loses its history the moment the branch merges and is deleted.

**If we did nothing**
Projects would keep multiplying per branch, the names would keep lying ("Main Branch" as identity), and finished branches would keep orphaning the record of what was built on them.

**The benefit**
One Yasystem can hold the refund stream, the picking-dashboard stream, and master work side by side — each ticket pointing at its branch — and the record survives every merge. This was the last building block: the tidy-up (one Websites system, Logistics folded into Yasystem, the rename) can now go ahead.

**2026-06-12 00:13 claude-code:** Review feedback from Austin actioned: the ticket Branch field now has a "Load branches" picker — it pulls the live branch list from GitHub (same read-only token mechanism as the project repo editor) and lists branches from the effective repo (the surface's own repo when it overrides, else the project's). Free typing still works for branches that don't exist yet (e.g. a t-numbered branch an agent is about to cut), and blank still means inherit. Pushed as 7735a2d — rides along with the next deploy.

---

**2026-06-12 01:44 — you**

done
