---
id: T-0352
title: Branch lives on the ticket — pin at scoping, or agent cuts a ticket branch (ADR-039)
type: feature
state: in_progress
created: 2026-06-10T15:00:28Z
updated: 2026-06-11T23:10:03Z
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
  - Ticket schema/types carry optional branch; editable in web ticket detail and via pm_update_ticket
  - pm_claim_ticket returns the effective branch (ticket ?? surface ?? project) and the policy together
  - "Convention documented in AGENTS.md: unset branch + protected default → agent cuts t<NNNN>-<slug> branch and records it on the ticket"
  - Sub-tasks inherit the parent ticket's branch when their own is unset
  - Yasystem project can drop 'Main Branch' from its name with no information lost (branch is data, not naming)
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
    status: in_progress
    progress:
      - at: 2026-06-11T23:10:03Z
        note: "Implementation complete: every ticket can now carry its own working branch, so several streams of work live inside ONE project instead of a project per branch. When the AI picks up a ticket it's told the one branch to work on — the ticket's own pin, else the parent ticket's (sub-tasks inherit), else the area's, else the project default — and on protected projects the rule is now written down: cut a ticket-named branch and record it back before committing, so the stream survives the branch's eventual merge. Branch is editable on the ticket page (with a hint showing what it inherits) and shows as a small tag on lists. Nine-scenario test suite passes; both typechecks clean. Adversarial review running before commit."
labels:
  - taxonomy
attention: null
version: 4
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