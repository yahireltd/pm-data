---
id: T-0318
title: Visual update to ticket conversations
type: feature
state: in_progress
priority: p2
created: 2026-06-09T16:54:26Z
updated: 2026-06-09T17:35:08Z
project: pm-tool-self
section: null
parent: null
children: []
order: 93184
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] visual serperation of user comments"
  - "[x] more modern visual style with uk date format and user icons"
out_of_scope: []
code_anchors:
  - path: web/app/_components/ConversationThread.tsx
    symbol: ConversationThread
  - path: web/app/_actions/conversation.ts
    symbol: splitConversation
  - path: web/app/_lib/formatting.ts
    symbol: formatCommentDate
  - path: web/app/_components/EditableMarkdownBody.tsx
    symbol: EditableMarkdownBody
  - path: web/app/tickets/[id]/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260609-1714
    model: claude-opus-4-8
    started: 2026-06-09T17:14:39Z
    status: completed
    ended: 2026-06-09T17:14:56Z
    summary: Gave the comments on a ticket a proper visual refresh. Comments used to run together as plain text lines divided by faint rules, which made a back-and-forth hard to follow. Each comment now sits in its own clearly separated card, led by a coloured circle showing who wrote it and the date in UK style (e.g. "9 Jun 2026, 14:03"). The thread now sits in its own Comments area above the box where you add a new comment. Nothing about existing comments was lost or rewritten — they just look better. If we'd done nothing, the conversation would have stayed cramped and harder to scan as threads grow.
    test_plan: '1. Open any ticket that already has comments (or add two via the "Add comment" box). 2. Confirm each comment shows as its own bordered card with an avatar circle (initials), the author name, and a UK-format date/time; hovering the date shows the full spelled-out date. 3. Confirm markdown inside a comment still renders (bold, headings, bullet lists, code). 4. Edit the Description (pencil → Edit body → Save) and confirm the existing comments are still there afterwards — they must NOT be dropped. 5. Stakeholder view (append ?view=stakeholder): confirm comments still render as cards. 6. A ticket with NO comments: confirm only the add-comment box shows, no empty thread. 7. Edge: a comment whose text contains a "---" line should still render as one comment, not split into two.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: agent
  reason: claude code is not using its avatar also users advatars are all grey they should get a random one that persists and have the ability to change in settings
  since: 2026-06-09T17:35:06Z
attachments:
  - key: tickets/T-0318/1781024079665-Screenshot_2026-06-09_at_17-53-28_pm-tool.png
    filename: Screenshot 2026-06-09 at 17-53-28 pm-tool.png
    content_type: image/png
    size: 107357
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-06-09T16:54:40Z
  - key: tickets/T-0318/1781025833565-Screenshot_2026-06-09_at_18-23-43_pm-tool.png
    filename: Screenshot 2026-06-09 at 18-23-43 pm-tool.png
    content_type: image/png
    size: 103783
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-06-09T17:23:54Z
  - key: tickets/T-0318/1781025840232-Screenshot_2026-06-09_at_18-23-25_pm-tool.png
    filename: Screenshot 2026-06-09 at 18-23-25 pm-tool.png
    content_type: image/png
    size: 159868
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-06-09T17:24:01Z
---

the conversaitons need a bit of a visual update to seperate user comments. Better more modern visual styling. uk date format and user icons - see attached screenshot (in ticket attachements) to see current look.

## Conversation

**2026-06-09 17:14 claude-code:** Run run-20260609-1714 completed — Gave the comments on a ticket a proper visual refresh. Comments used to run together as plain text lines divided by faint rules, which made a back-and-forth hard to follow. Each comment now sits in its own clearly separated card, led by a coloured circle showing who wrote it and the date in UK style (e.g. "9 Jun 2026, 14:03"). The thread now sits in its own Comments area above the box where you add a new comment. Nothing about existing comments was lost or rewritten — they just look better. If we'd done nothing, the conversation would have stayed cramped and harder to scan as threads grow.

**2026-06-09 17:30 claude-code:** Thanks — both screenshots pointed at the same root cause. Comments get written in two slightly different shapes depending on whether they come from the website or from an automated agent action (like a "run completed" note). The new design only recognised the website shape, so an agent-written comment either showed as plain unstyled text (your first screenshot) or, when it sat alongside a website comment, didn't appear as its own card at all (your second screenshot).

Fixed so both shapes are recognised — every comment now renders as its own card with avatar and UK date, regardless of who or what wrote it. Pushed; please pull/redeploy and re-check the two cases from your screenshots.
