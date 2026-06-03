---
id: ADR-004
slug: stakeholder-polymorphism
title: Single stakeholders[] array on every entity, not separate join tables
state: accepted
decided: 2026-04-26
decided_by: [austin]
project: pm-tool-self

supersedes: null
superseded_by: null

tickets: []
---

# ADR-004: Stakeholder polymorphism

## Context

We need to attach "people who care" to tickets, projects, and meetings. ya-hire's contracts-comms feature took the procedural approach: a 250-line private method that resolves stakeholders from 5 different tables (`ya_contract_contacts`, `quote_phone_numbers`, `ya_customer_contact`, salespersons, activity loggers). It works but it's not reusable across entity types and there's no single place to add "remind me when X changes."

Two design options for pm-tool:

1. **Polymorphic join.** A `stakeholders` table with `entity_type` + `entity_id`. One canonical place to query "give me all stakeholders of any entity type."
2. **Embedded array.** Each entity's frontmatter has its own `stakeholders[]` array, fully self-contained.

## Decision

**Embedded array.** Every ticket, project, and meeting carries its own `stakeholders[]` directly in frontmatter.

Rationale:
- pm-tool's substrate is markdown files in git, not a relational DB. A polymorphic join would require a separate index that drifts from the source of truth.
- Stakeholders are *contextual* — Sam at Acme is a stakeholder on T-0099 because of that specific ticket, not as a global property of "Sam."
- The git diff for a ticket showing "added stakeholder X" is more useful than a separate table change.
- Scanning all entities to find "what does Sam care about?" is cheap (linter already walks `.pm/` building an index).

## Consequences

**Positive:**
- One file = one entity, including who cares about it. No joins.
- Easy to lift entire entities (export, archive, hand off).
- Stakeholder shape can drift per-entity if needed (e.g. meetings might want a `is_required` field).
- Web UI can read stakeholders without a second fetch.

**Negative:**
- Same person across 50 tickets means 50 copies of their contact info. Acceptable for v1; add a `contact_ref:` indirection if it gets painful (resolves at notification time from `.pm/.private/contacts.yml`).
- Renaming a person ("Sam Kelly" → "Samantha Kelly") requires a sweep. The CLI gets a `pm contact rename` command for v1.1.
- No O(1) "what does Sam care about?" — needs an index walk. The linter's index already covers this; expose it as a CLI command (`pm whose <name>`).

## Alternatives considered

- **Hybrid (master contact list + per-entity refs by id).** Cleaner for renames but adds an indirection that hurts the "one file = one entity" property. Defer to v2 when the pain is real.
- **External CRM.** Out of scope — pm-tool stays self-contained.
