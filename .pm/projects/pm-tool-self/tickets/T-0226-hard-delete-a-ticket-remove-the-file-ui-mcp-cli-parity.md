---
id: T-0226
title: Hard-delete a ticket (remove the file) — UI + MCP + CLI parity
type: feature
state: triaged
created: 2026-06-05T13:37:13Z
updated: 2026-06-05T13:37:13Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - A ticket can be permanently deleted (its markdown file is removed) from the web UI, behind an admin-only control with a confirmation step
  - The same delete is available over MCP via a pm_delete_ticket tool and at CLI parity
  - After delete, the ticket no longer appears in any list/board and the project ticket counts are revalidated
  - Deletion is rejected (or clearly warned) for a non-existent id, and dangling link references are handled or surfaced
out_of_scope: []
code_anchors:
  - path: web/app/_actions/tickets.ts
  - path: mcp-server/src/
  - path: cli/src/index.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - meta
attention: null
---

## Problem

There is no way to **delete** a ticket from any surface. The state machine supports soft-dispose (`wontfix`/`duplicate`) and the web `setTicketState` action already accepts them, but a disposed card still lingers on the board, and there is no hard-delete (file removal) anywhere — not in the web UI, not over MCP, not in the CLI.

This bites in dogfooding: scratch/junk tickets created while testing (e.g. T-0207 "dog shit scooop", T-0205 "test promote bittpn", T-0209 self-test artifact) cannot be cleaned up via the agent surface and clutter the backlog.

## Decision

Add a **hard delete** that removes the ticket markdown file entirely (chosen over soft-dispose-only — junk should leave no card). This intentionally trades away the filesystem audit trail for those tickets (cf. ADR-001 filesystem-first), so it is gated: admin-only + an explicit confirmation.

## Scope

- Core lib `deleteTicket(id)` — locate the file via the existing resolver and unlink it; bump/revalidate the project.
- Web server action + an admin-only Delete control on the ticket page, behind a confirm dialog.
- MCP tool `pm_delete_ticket`.
- CLI parity (`pm ticket delete <id>` / equivalent).

## Guardrails

- Admin-only (web RBAC); confirmation required before the irreversible removal.
- Consider warning when the ticket is referenced by links (duplicate_of/parent/related) so we don't leave dangling refs.