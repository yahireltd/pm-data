---
id: ADR-025
slug: mcp-write-parity-agents-mutate-through-dedicated-tools-that-
title: "MCP write-parity: agents mutate through dedicated tools that mirror web/CLI invariants exactly, not a generic file-write"
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0152
  - T-0194
  - T-0198
  - T-0199
---

## Context
Agents initially could only READ pm data over MCP; creating/editing happened via web or CLI. To let agents do real PM hygiene (file tickets, milestones, decisions, sprints) we faced a choice: (a) expose a generic 'write this markdown/frontmatter' tool, which is maximally flexible but lets agents produce records that bypass the schema rules, id-allocation, and templates the web/CLI enforce; or (b) build a dedicated, typed tool per mutation.

## Decision
Adopt per-mutation MCP write tools (pm_create_ticket, pm_update_ticket, pm_create_milestone, pm_create_decision, pm_create_sprint, pm_commit/uncommit_ticket, pm_set_backlog_fields, pm_create_pre_project, tech-session tools, etc.). Each one is written to mirror its web/CLI counterpart EXACTLY — same per-project id allocation, same defaults, same body templates (e.g. create-decision copies templateForKind byte-for-byte), and the same invariants (commit requires acceptance criteria; complete-sprint requires velocity). An MCP-created record is indistinguishable from a web-created one.

## Consequences
Every new mutation now needs a third implementation kept in lockstep with web and CLI, and parity drift is a standing maintenance risk. We accepted that duplication-of-logic cost to guarantee that agent-authored records are schema-valid, correctly id'd, and obey the same gates as human-authored ones — there is no privileged back door that skips the rules, which is what filesystem-first integrity (ADR-001) depends on.