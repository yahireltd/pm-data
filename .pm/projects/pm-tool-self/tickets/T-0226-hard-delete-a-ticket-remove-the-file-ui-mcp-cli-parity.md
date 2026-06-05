---
id: T-0226
title: Hard-delete a ticket (remove the file) — UI + MCP + CLI parity
type: feature
state: review
created: 2026-06-05T13:37:13Z
updated: 2026-06-05T15:41:41Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude
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
agent_runs:
  - id: run-20260605-1541
    model: claude-opus-4-8
    started: 2026-06-05T15:41:30Z
    status: completed
    ended: 2026-06-05T15:41:41Z
    summary: "You can now permanently delete a junk ticket. Until now there was no way to remove a ticket at all — the closest was marking it \"won't fix\", which still left it cluttering the board. Now an admin can delete a throwaway ticket outright, with a confirmation step, and it's available everywhere: in the app, through the agent tools, and on the command line. We used it to clear three leftover test tickets. The deliberate trade-off is that a deleted ticket is gone for good rather than archived, which is why it's limited to admins and asks for confirmation first."
    test_plan: As an admin, open a scratch ticket and use Delete in the Danger zone — confirm the prompt, then it disappears and you're returned to the home screen. As a non-admin, confirm there is no delete control. On the command line, `pm delete <id>` refuses unless you add --force. Deleting a ticket that other tickets linked to should leave no broken links.
    records:
      docs: none-needed
      tech_session: TS-007
      status_note: none-needed
labels:
  - dogfood
  - meta
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T15:41:41Z
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

## Conversation

**2026-06-05 13:59 claude-code:** Shipped across all four surfaces and deployed to prod.

**Commits** (pushed to master, `pm-deploy` green twice):
- `3e1263f` — hard-delete across web (admin-only `deleteTicket` action + two-step `DeleteTicketButton` in the ticket sidebar Danger zone), MCP (`pm_delete_ticket`, guarded by `confirm:true`), and CLI (`pm delete <id> --force`). All three strip dangling references to the deleted id out of other tickets' link fields before unlinking.
- `926d70d` — fix: the `pm delete` HELP line used backticks inside the backtick-delimited HELP template literal, a syntax error that crashed the `pm` CLI binary (web/MCP services were unaffected — separate codepaths, which is why the web build type-gate passed on the first push). Caught during CLI smoke-test.

**Verification:**
- Web build (the type-gate) compiled clean both times; `pm-tool` + `pm-mcp` restarted active.
- CLI: `pm help` renders the delete line, and `pm delete T-0209` (no `--force`, against live PM_ROOT) gives the graceful refusal `Refusing to delete T-0209 without confirmation...` with the resolved file path. Destructive path not exercised on real data.
- MCP `pm_delete_ticket` is registered and deployed; it won't hot-load into an existing MCP client session until a restart (known gotcha).

**Design notes:** hard delete (file removal) was chosen over soft-dispose-only — junk should leave no card. This forfeits the ADR-001 filesystem audit trail for deleted tickets by design, so the web path is admin-only via effective identity (an admin previewing-as a non-admin is correctly blocked per ADR-009). Soft-dispose (`wontfix`/`duplicate`) on the StateSelector is untouched.

Ready for human review/close. Scratch tickets T-0207/T-0205/T-0209 can now be cleaned up.

**2026-06-05 15:41 claude:** Run run-20260605-1541 completed — You can now permanently delete a junk ticket. Until now there was no way to remove a ticket at all — the closest was marking it "won't fix", which still left it cluttering the board. Now an admin can delete a throwaway ticket outright, with a confirmation step, and it's available everywhere: in the app, through the agent tools, and on the command line. We used it to clear three leftover test tickets. The deliberate trade-off is that a deleted ticket is gone for good rather than archived, which is why it's limited to admins and asks for confirmation first.
