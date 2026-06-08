---
id: T-0305
title: Font size adjuster
type: feature
state: review
priority: p2
created: 2026-06-08T12:20:56Z
updated: 2026-06-08T14:21:58Z
project: pm-tool-self
section: null
parent: null
children: []
order: 92160
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - We need to set a min fontsize and the other fontsizes be linked to the min font size and scale?
out_of_scope: []
code_anchors:
  - path: web/app/_lib/font-scale.ts
  - path: web/app/_components/FontScaleControl.tsx
  - path: web/app/layout.tsx
  - path: web/app/me/page.tsx
  - path: web/app/globals.css
    lines: 42-46
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1419
    model: claude-opus-4-8
    started: 2026-06-08T14:19:55Z
    status: completed
    ended: 2026-06-08T14:21:58Z
    summary: "People can now set how large text appears across the whole app, from their \"My work\" page. We added a \"Text size\" choice (Small, Default, Large, Larger, Largest) — picking one instantly resizes everything in the interface together, not just one area, because all the app's text is sized relative to one base value that this setting scales. The choice is remembered on that person's device and re-applied the moment a page loads, with no flicker of the old size. This matters for accessibility and comfort: anyone who finds the default text too small (or wants it more compact) can tune it themselves without an admin or a code change, and big flow-diagram-heavy screens become easier to read. Without it, text size was fixed for everyone and the only way to change it was editing the code."
    test_plan: |-
      1. Sign in → open "My work" (/me). A "Text size" card shows five options; the current one is highlighted, and each chip previews its own size.
      2. Click "Larger" → the entire app's text grows immediately (sidebar, headings, body, etc., all scale together).
      3. Navigate to another page, then reload → the larger size persists with no flash of the default size on load.
      4. Set it back to "Default" → returns to the original 17px base.
      5. Open the app in a different browser/device → it's independent (the setting is per-device), back at Default there.
      6. Open /help → the "My work" section now mentions the Text size setting.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Updated /me help content (web/app/help/content/me.ts) to describe the new Text size setting.
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-08T14:21:58Z
---

Crete a settings area with a font size adjuster per user

## Conversation

**2026-06-08 14:21 claude:** Run run-20260608-1419 completed — People can now set how large text appears across the whole app, from their "My work" page. We added a "Text size" choice (Small, Default, Large, Larger, Largest) — picking one instantly resizes everything in the interface together, not just one area, because all the app's text is sized relative to one base value that this setting scales. The choice is remembered on that person's device and re-applied the moment a page loads, with no flicker of the old size. This matters for accessibility and comfort: anyone who finds the default text too small (or wants it more compact) can tune it themselves without an admin or a code change, and big flow-diagram-heavy screens become easier to read. Without it, text size was fixed for everyone and the only way to change it was editing the code.
