---
id: TS-013
slug: designing-the-plain-language-user-help-layer-a-help-button-o
title: Designing the plain-language user help layer (a "Help" button on every screen + the /help guide)
project: pm-tool-self
created: 2026-06-06T01:20:32Z
updated: 2026-06-06T01:22:10Z
decisions:
  - "Two layers of help, kept separate from the developer wiki. We kept the existing quick hover-tooltip (a one-line hint that appears when you point at a title) and added a fuller \"Help\" button that opens a side panel with the whole screen's help. They do different jobs — a glance versus a proper read — so both earn their place. We also kept this new help completely separate from the developer wiki: the wiki is for people building the tool, the new guide is for people using it. Trying to serve both audiences from one place is what made the old documentation feel developer-only."
  - "One help file per screen, stored with the code, plus a central index that maps each screen to its help. Every screen's help is its own small file that travels with the app and ships on each deploy. We chose this over keeping the help in a database or in loose separate documents because: it is checked for mistakes alongside the rest of the code, it lives in the same place as the screen it describes (so it is hard to forget when that screen changes), and it needs no extra moving parts to load. The central index also lets any screen work out and show the right help on its own."
  - One reusable "Help" button used everywhere, in two modes. A single button is dropped into every screen. On most screens it is told exactly which help to show. On the project tabs it works out the right help from the page address by itself — and because of that "work it out" mode, a single change to the shared project header gave all fourteen project tabs (and the decision and meeting detail screens) their help at once, instead of editing each one. The button quietly shows nothing if a screen has no help yet, so it is always safe to add.
  - Open to everyone signed in, but never to the signed-out. The guide sits behind the same secure sign-in as the rest of the tool, but unlike the developer wiki — which only the build team can see — it is readable by every signed-in person, including read-only stakeholders, because they are exactly who benefits most from plain help. A signed-out visitor is always sent to the sign-in page first. We folded this into the existing access check as a clean rule rather than a special case, so it reads clearly and can't accidentally open a hole.
  - "Wrote two screens by hand as the gold standard, then generated the other thirty from the real screens. We hand-wrote the overview page and one screen first to lock down the voice and the shape. Then a team of agents drafted the rest in parallel — each one reading the actual screen's code and fact-checking its own draft against it, in two passes (write, then verify). That caught real errors a generic writer would have invented: for example, a project card that shows a project's status was wrongly described as showing its stage, and a \"gone quiet\" warning that triggers after ten days had been described as about a week. The lesson: ground generated documentation in the real source and make it check itself, or it reads plausibly and is quietly wrong."
  - Made "keep the help current" a written rule, not a hope. We added a rule to the agent guidebook (and mirrored it in the developer wiki) that a screen's help must be updated in the same change as the screen itself, and that a brand-new screen ships with its own help. This is the single thing that stops help from drifting into lies over time — the same principle the project already applies to its other documentation.
  - 'Lesson from a bug we shipped and fixed: a contextual help control has to be ONE distinct, clearly-labelled, consistently-placed button — not a second symbol next to an existing one. The first version dropped a bare "?" right beside the existing hover-tooltip "?" on the handful of screens with custom headers, so you saw two or three "?" jammed together and it looked broken. The fix was to use the same single, right-aligned "Help" button on every screen, matching the screens that already looked right. The takeaway: visual consistency across screens matters more than making the smallest possible edit on each one.'
  - "Lesson on safety before deploy: because the help is stored as plain data files, we could load and check all thirty-two of them locally to catch mistakes before pushing — even though this setup has no local code-checker and normally the server build is the first thing to catch an error. That gave real confidence the help layer wouldn't break the build. The limit: full screen code still only gets fully checked by the server build, so we stayed careful and precise on the screen edits themselves."
actions:
  - text: Take a final live look at the new help (open a few screens, press Help, check the guide, confirm a signed-out visitor is bounced) and close the help-feature ticket.
    ticket: T-0271
side_quests:
  - "Polish ideas, parked: (1) collapse the small hover-tooltip into the \"Help\" button on screens where having both feels redundant; (2) one detail screen (a pre-project) still uses the small \"?\" icon rather than the labelled button — standardise it; (3) if it ever matters, load only the current screen's help instead of bundling all of it wherever a Help button appears."
problem: People running the work couldn't learn the tool from inside the tool. The only in-app documentation was the developer wiki — written for people building pm-tool, not using it — so anyone non-technical felt locked out. We needed help on every screen, in plain language, that stays accurate as the tool changes, without it becoming a second pile of documents that quietly drifts out of date.
success_criterion: Every screen has a "Help" button that opens plain-language guidance for that exact screen; a browsable guide gathers it all in one place; it sits behind sign-in but is readable by everyone signed in (including read-only stakeholders); and there is a standing rule that keeps each screen's help updated whenever the screen changes.
phase: test
---

# TS-013: Designing the plain-language user help layer (a "Help" button on every screen + the /help guide)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
