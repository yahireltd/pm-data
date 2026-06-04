---
id: ADR-017
slug: hand-rolled-inline-styled-html-email-shell-with-a-bespoke-ma
title: Hand-rolled inline-styled HTML email shell with a bespoke markdown→HTML renderer
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0186
---

## Context
Outbound comms (meeting/ticket notifications, reminders, relays) were plain text and off-brand. We wanted branded HTML matching the pm-tool UI, rendered from the markdown our templates already use. The obvious path is a markdown library plus an MJML/templating framework. But email clients (Outlook/Gmail/Apple Mail) strip <style>/<head> and ignore class selectors, so any solution must emit fully inline-styled, table-based HTML — and pulling a general renderer would add a dependency whose output we'd have to post-process and re-inline anyway, for the narrow subset of markdown our templates actually use.

## Decision
Hand-roll it in the email channel. A small markdown→HTML renderer escapes HTML then converts exactly the constructs the templates use — bold, code, links (relative URLs absolutized via AUTH_URL), blockquotes, and bullet lists — emitting an inline style on every element. The fragment is wrapped in a table-based shell (accent header bar, white card, muted footer) with the pm-tool palette, every rule inline. No markdown or email-layout library is added.

## Consequences
Branded, client-robust email with zero new dependencies and full control over the inlined output. The cost: the renderer supports only the markdown subset we hand-coded (anything richer renders as escaped text), and the styling is duplicated inline rather than shared with the web UI's CSS — brand changes mean editing the shell by hand, and the renderer must be kept in lockstep with whatever markdown the templates start using.