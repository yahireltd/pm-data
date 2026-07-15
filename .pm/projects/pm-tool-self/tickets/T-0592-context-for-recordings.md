---
id: T-0592
title: Context for recordings
type: feature
state: done
priority: p2
created: 2026-07-15T14:30:17Z
updated: 2026-07-15T15:31:58Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 120832
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] pm_get_meeting returns series_context: the previous occurrence(s) in a recurring chain (up to 3, via the follows links) with their outcomes — so last week's commitments arrive in the same call as this week's transcript"
  - "[x] AGENTS.md §4 instructs agents to gather context BEFORE drafting outcomes from a transcript: series_context for recurring/org meetings; project charter + open tickets + stakeholders for project meetings"
  - "[x] AGENTS.md §4 also requires surfacing what was NOT decided: agenda items with no resolution are proposed as 'Not resolved — carried forward' outcomes, which roll into the next occurrence's series_context (per Ben conversation, 15 Jul)"
  - "[x] Works for org meetings (project='org') and project meetings alike; a broken/cyclic follows chain degrades gracefully"
  - "[x] Outcome drafts remain proposals for human review — context informs them, nothing auto-commits"
out_of_scope: []
code_anchors:
  - path: mcp-server/src/tools/get-meeting.ts
    symbol: series_context
  - path: AGENTS.md
    symbol: §4 Meetings — transcript context rule
relates:
  - T-0513
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1503
    started: 2026-07-15T15:03:44Z
    status: completed
    ended: 2026-07-15T15:06:16Z
    summary: "Agents now get the background they need before turning a meeting transcript into outcomes. A transcript is only sound turned into text — on its own, \"Costin\" is just a name and \"the testing\" could be anything. Two changes close that gap. First, when an agent fetches a recurring meeting (like the weekly M-010), it now automatically receives the previous few occurrences in the series with their outcomes — so it can see what was committed last week and spot whether it moved, without extra steps. Second, the agents' rulebook now requires gathering context BEFORE interpreting any transcript: for recurring/org meetings, read the series history; for project meetings, load the project's charter, open tickets and stakeholder list first so people and topics resolve to real things. Outcomes are always drafted with an owner and due date where one was said, and remain proposals for you to review — context sharpens them, nothing auto-commits. If we did nothing, transcript-derived outcomes would stay generic and occasionally wrong about who/what. Benefit: the weekly record→transcribe→outcomes loop gets smarter every week because each meeting reads its own history."
    test_plan: "1. Deploy (restarts pm-mcp; no session restart needed — pm_get_meeting's inputs are unchanged, only its response is richer). 2. In a session, ask an agent for M-010 via pm_get_meeting (project=\"org\"): the response now carries series_context — once M-010 has a scheduled-next occurrence (T-0580), fetching THAT meeting shows M-010's outcomes (the Ben-call items) in its series_context. 3. Record next week's meeting, transcribe, then ask an agent to draft outcomes: it should reference last week's commitments (e.g. \"Rob's testing push — did it happen?\") because the rulebook now tells it to read the series first. 4. For a PROJECT meeting transcript, the agent should visibly load the project (charter/tickets/stakeholders) before proposing outcomes. 5. Edge: a one-off meeting returns an empty series_context; a broken follows link doesn't error — just less context."
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: AGENTS.md §4 — the transcript-context rule agents are served
labels: []
attention: null
version: 15
---

For meetings outside of a project, the context should come from previous meetings (if they have followups) 

For project meetings- when recording outcomes the agent should get some project context first before recording outcomes as it may help decide what we are talking about and why

## Conversation

**2026-07-15 15:06 claude-code:** Run run-20260715-1503 completed — Agents now get the background they need before turning a meeting transcript into outcomes. A transcript is only sound turned into text — on its own, "Costin" is just a name and "the testing" could be anything. Two changes close that gap. First, when an agent fetches a recurring meeting (like the weekly M-010), it now automatically receives the previous few occurrences in the series with their outcomes — so it can see what was committed last week and spot whether it moved, without extra steps. Second, the agents' rulebook now requires gathering context BEFORE interpreting any transcript: for recurring/org meetings, read the series history; for project meetings, load the project's charter, open tickets and stakeholder list first so people and topics resolve to real things. Outcomes are always drafted with an owner and due date where one was said, and remain proposals for you to review — context sharpens them, nothing auto-commits. If we did nothing, transcript-derived outcomes would stay generic and occasionally wrong about who/what. Benefit: the weekly record→transcribe→outcomes loop gets smarter every week because each meeting reads its own history.

**2026-07-15 15:11 claude-code:** Scope addition from Austin's conversation with Ben (commit faf9a26): agents interpreting a transcript must now also surface what was NOT decided. The rulebook tells them to read the transcript against the meeting's agenda and propose any unresolved agenda item as a "Not resolved — carried forward" outcome, with an owner where one is named. Because recurring meetings carry previous outcomes into the next occurrence (series_context, this ticket), an open question now resurfaces automatically every week until someone closes it — it can't silently evaporate between meetings. The heavier structural version (docketed decisions that nag until dispositioned) remains T-0513, now linked as related. Re-test on the next recorded meeting: the drafted outcomes should include both what was agreed AND a clearly-marked list of agenda items that didn't get resolved.

---

**2026-07-15 15:31 — you**

Done and working fine
