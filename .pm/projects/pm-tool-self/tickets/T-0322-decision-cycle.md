---
id: T-0322
title: Decision cycle
type: feature
state: done
priority: p2
created: 2026-06-09T19:20:55Z
updated: 2026-06-22T15:48:24Z
project: pm-tool-self
section: null
parent: null
children: []
order: 95232
reporter: null
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
attachments:
  - key: tickets/T-0322/1781042073447-Screenshot_2026-06-09_at_22-54-25_Sales_process_analysis_framework_-_Claude.png
    filename: Screenshot 2026-06-09 at 22-54-25 Sales process analysis framework - Claude.png
    content_type: image/png
    size: 44126
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-06-09T21:54:34Z
---

Just thinking about how to analyse the sales process there are lots of things to make but the tools can be figured out by asking. What do we need to see (visibility) -> That will lead to a decision (decision) -> An action will be made from that decison (action / process) -> an outcome is expected from that decision (outcome) -> That outcome needs a ( review ) and that review will lead to another decision and the cycle continues.

To capture what is needed we can get the users to come up with what they need to see, what decisions need to happen due to that, the actions and proecess to carry out those decisions, and how they will be reviewed (success criteria) we should come up with a nice way to scope relevant parts of projects in this way. when making user facing solutions

## Conversation

**2026-06-09 23:27 claude-code:** **The decision-cycle method just had its first real-material test — against the sales process Freeform board (PP-002) — and it passed convincingly.**

**The headline finding:** the board already half-follows the cycle without naming it. Every scenario card states the problem, the decisions needed, and a workflow, and most flows end in "review of results via dashboards and meetings". What the cards don't systematically capture is exactly what this framework adds: the explicit **what must we SEE to make that decision**, and a concrete **outcome + who reviews it, when, against what**. The method names the gaps in real scoping material — which is the job.

**The board's content, re-expressed as decision cycles (see → decide → act → outcome → review):**

1. **Lead routing** — see: AI type + score, value thresholds → decide: which conversion process (system-only / quick / in-depth / lifetime / escalate) → act: route + assign → outcome: right-sized handling, closing up → review: score accuracy + conversion by route; corrections feed the model.
2. **Stop-chasing handover** — see: quote age, follow-up history → decide: when does a human stop and the system take over → act: 'stop' triggers system follow-ups → outcome: stopped quotes convert at low cost → review: stopped-pool conversion + cost.
3. **Big-fish alerting** — see: high-potential arrival alert + unprocessed list → decide: manager picks person + process → act: instruction to AM/SE → outcome: big customers greeted fast at the right level → review: list held until convert-or-cease.
4. **Account level + momentum** — see: actual-vs-potential grid, account statuses → decide: what level, moving toward potential? → act: assign ownership + plan per level → outcome: lifetime value converted → review: quarterly transfer windows.
5. **System-estate stewardship** — see: KPIs of the system-managed pool → decide: which en-masse interventions → act: steward runs remarketing / membership pushes → outcome: targets hit on unowned revenue → review: steward's regular results review.
6. **Leakage / conformance** — see: conformance dashboards, missed-opportunity view → decide: where is leakage → act: manager reassessment, dip-tests → outcome: leakage reduced → review: dashboards + meetings.

The board's open questions (BANT per level, value thresholds, "do we agree these levels?") all land in the decision column as criteria-decisions the scoping sessions must settle first.

**Where this lands:** PP-002's goals + success criteria are now filled from these cycles (the review column literally becomes the success criteria — the framework's promised payoff). **Recommended build path:** keep using it as a method (template + scoping-meeting agenda) on PP-002; once it has survived contact with the team sessions, promote it to a charter section — rows of see/decision/action/outcome/review, each row spawnable into tickets with the review becoming acceptance criteria.

---

**2026-06-22 15:48 — you**

done

---

**2026-06-22 15:48 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
