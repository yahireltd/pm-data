---
id: T-0231
title: Human friendly ticket closes
type: feature
state: review
priority: p2
created: 2026-06-05T15:28:06Z
updated: 2026-06-05T15:40:58Z
project: pm-tool-self
section: null
parent: null
children: []
order: 63488
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - As per the description just need to upate agents.md i think
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1528
    model: claude
    started: 2026-06-05T15:28:39Z
    status: completed
    summary: "Agents now write the notes that humans read — ticket updates, closing notes, session records, comments — in plain, everyday language instead of technical jargon. Each write-up covers four simple things: what we did, why we did it, what would have happened if we did nothing, and the benefit. This means non-technical stakeholders can actually follow what's going on in their projects, rather than seeing developer shorthand. The rule lives in the agent guide and also appears right in the fields agents fill in, so it's hard to forget."
    ended: 2026-06-05T15:40:58Z
    test_plan: Read this very ticket's summary and the two new decision records (ADR-029, ADR-030) as a non-technical person — they should be understandable with no jargon or file names. Going forward, new ticket/closing notes from agents should follow the what/why/cost-of-inaction/benefit shape.
    records:
      docs: updated
      tech_session: TS-007
      status_note: none-needed
      docs_note: AGENTS.md gained section 11; the in-app dev wiki Conventions section mirrors it; the plain-language reminder was added to the agent tool fields (complete-run summary, comment body, tech-session).
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T15:40:58Z
---

When writing up notes for tickets can the agents do it in more non tech language so the steakholders can understand.

We want to say what we did, What problem did it solve or why we did it. What would happened if we did nothing and the benefits of what we did, Anytime agents update tech sessions / tickets we should be clear and use easy to understand language

## Conversation

**2026-06-05 15:40 claude:** Run run-20260605-1528 completed — Agents now write the notes that humans read — ticket updates, closing notes, session records, comments — in plain, everyday language instead of technical jargon. Each write-up covers four simple things: what we did, why we did it, what would have happened if we did nothing, and the benefit. This means non-technical stakeholders can actually follow what's going on in their projects, rather than seeing developer shorthand. The rule lives in the agent guide and also appears right in the fields agents fill in, so it's hard to forget.
