---
id: T-0594
title: Review page counts brand-new inbox/scoping tickets — Review should mean review of work done
type: bug
state: done
created: 2026-07-15T15:55:56Z
updated: 2026-07-15T17:50:10Z
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
  name: claude-code
acceptance_criteria:
  - "[x] Inbox and triaged tickets never appear on the Review page nor count in its badge, attention flag or not"
  - "[x] State=review tickets always appear; attention-flagged tickets in ready/in_progress/at_risk still appear (failed-run alerts aren't lost)"
  - "[x] The page and the top-bar badge use ONE shared rule (no duplicated logic to drift)"
  - "[x] The 'Unassigned' section disappears for inbox arrivals (inbox tickets have no project)"
out_of_scope: []
code_anchors:
  - path: web/app/_lib/review.ts
    symbol: needsReview (new shared rule)
  - path: web/app/review/page.tsx
  - path: web/app/layout.tsx
    symbol: review badge
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1556
    started: 2026-07-15T15:56:19Z
    status: completed
    ended: 2026-07-15T15:59:02Z
    summary: "The Review page now means what you expect: verdicts on finished work — nothing else. Before, any ticket carrying an attention flag counted as \"review\", and since emails arriving at support create tickets that are flagged from birth (\"needs response\"), brand-new inbox arrivals and things still being scoped piled up in an \"Unassigned\" section at the bottom of Review and inflated its red badge. Now the rule is: tickets in the review state always count (an agent finished, you confirm or send back), and attention flags only count once work is actually in flight — so a failed agent run still reaches you, but new arrivals stay on the Inbox, which is where they already show and where scoping belongs. The page and the menu badge now share one single rule in code, so they can never disagree again (previously they were two copies kept matching by a comment). If we'd left it, the Review count would keep overstating what's waiting on you and the page would keep mixing two different kinds of work. Benefit: Inbox = to be scoped/done, Review = to be judged — exactly your model."
    test_plan: '1. After deploy, open Review: the bottom "Unassigned" section of inbox arrivals is gone; only real review items (and any flagged in-flight work) remain, and the badge count matches the page. 2. Send a fresh email to support@ → the new ticket appears on the INBOX page/badge but NOT on Review. 3. Triage that ticket (still not on Review), mark it ready and have an agent fail a run on it (or just note: attention on ready/in_progress still shows) — flagged in-flight work still surfaces. 4. Complete any agent run → the ticket enters review state and appears as before. 5. The menu badge over a few page loads always equals the number of rows the Review page shows.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ux
  - dogfood-finding
attention: null
version: 10
---

## Problem

The Review page (and its menu badge) include ANY ticket with an attention flag. Inbound-email tickets arrive in the inbox already flagged ("needs response"), so brand-new/being-scoped tickets show in an "Unassigned" section at the bottom of Review and inflate the badge. Austin's model: Inbox = work to be scoped/done; Review = review of work DONE.

## Fix

One shared needsReview() rule used by BOTH the page and the top-bar badge (they had duplicated logic, kept in sync by comment only): state === "review" always counts; an attention flag only counts when the ticket is past the pre-work states (not inbox/triaged). Inbox arrivals stay on the Inbox page/badge where they already surface; in-flight alerts (e.g. a failed agent run on a ready ticket) still reach Review.

## Conversation

**2026-07-15 15:59 claude-code:** Run run-20260715-1556 completed — The Review page now means what you expect: verdicts on finished work — nothing else. Before, any ticket carrying an attention flag counted as "review", and since emails arriving at support create tickets that are flagged from birth ("needs response"), brand-new inbox arrivals and things still being scoped piled up in an "Unassigned" section at the bottom of Review and inflated its red badge. Now the rule is: tickets in the review state always count (an agent finished, you confirm or send back), and attention flags only count once work is actually in flight — so a failed agent run still reaches you, but new arrivals stay on the Inbox, which is where they already show and where scoping belongs. The page and the menu badge now share one single rule in code, so they can never disagree again (previously they were two copies kept matching by a comment). If we'd left it, the Review count would keep overstating what's waiting on you and the page would keep mixing two different kinds of work. Benefit: Inbox = to be scoped/done, Review = to be judged — exactly your model.

---

**2026-07-15 17:50 — you**

Done
