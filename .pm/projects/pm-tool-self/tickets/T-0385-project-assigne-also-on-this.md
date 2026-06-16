---
id: T-0385
title: project assigne / also on this
type: feature
state: done
priority: p2
created: 2026-06-16T14:20:18Z
updated: 2026-06-16T16:16:37Z
project: pm-tool-self
section: null
parent: null
children: []
order: 100352
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] When in the ticket view,  assigning an asignee / also on this should use the project register (type / autofill)"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260616-1421
    model: claude
    started: 2026-06-16T14:21:59Z
    status: in_progress
    summary: Claimed via web UI
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 18
branch: master
collaborators:
  - kind: human
    name: Austin
---

both need to use the project register

## Conversation

**2026-06-16 15:06 claude-code:** **Done — the assignee and "also on this" fields now use the project register.**

**What changed:** When you set the assignee or add someone to "also on this" on a ticket, you now get a type-ahead dropdown that suggests the people and agents on this project, instead of typing a free-form name. Suggestions come from the **project register** — the project's stakeholders and team members, plus the AI agents (Claude, etc.) — and for a ticket with no project it falls back to the shared org roster of everyone you've added before. Picking a suggestion fills in the name (and, for "also on this", whether they're a person or an agent) in one click. You can still type a name by hand if someone isn't on the register yet.

**Why:** It was too easy to mistype a name or add the same person under two slightly different spellings, which made it hard to tell at a glance who's on a piece of work. Drawing from one known list keeps names consistent and makes assigning quicker.

**Benefit:** Faster, mistake-proof assignment; the same person always reads the same way across the tool; and agents are first-class in the picker so you can hand work to Claude as easily as to a teammate.

**Status:** Code is committed to master and pushed. It will appear on the live site after the next deploy.

**2026-06-16 16:07 claude-code:** **Review feedback addressed — both points fixed.**

1. **Assignee can now pick humans as well as agents.** Before, on a project that didn't yet have any team members or stakeholders listed, the assignee dropdown only offered the AI agents. The register now always includes every person the tool knows — anyone you've added before (the shared roster) plus the tool's admins — so you can assign a real person on any ticket, not just an agent.

2. **"Also on this" now filters by the Person/Agent choice.** The Person/Agent selector sits in front of the name box and drives the suggestions: choose **Person** and only people are suggested; choose **Agent** and only agents are. So you won't see agents mixed in when you're adding a person.

Committed and pushed to master (a15296a). It'll be on the live site after the next deploy.

---

**2026-06-16 16:16 — you**

Confirmed working as expected
