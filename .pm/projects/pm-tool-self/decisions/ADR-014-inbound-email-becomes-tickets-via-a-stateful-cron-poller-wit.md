---
id: ADR-014
slug: inbound-email-becomes-tickets-via-a-stateful-cron-poller-wit
title: Inbound email becomes tickets via a stateful cron poller with conversation+subject-tag threading
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0177
  - T-0180
---

## Context
To accept support requests by email we needed inbound mail to land as .pm tickets and stay threaded as the conversation continued. Real-time options (Graph change-notification webhooks, or an always-on IMAP/SMTP service) add a public callback endpoint, subscription renewal, and a long-lived process — heavy for a filesystem-first tool. We also had to avoid duplicate tickets when the same mailbox is re-polled, and thread replies even when a reply lands in a different Graph conversation than the original (e.g. a reply to a forward).

## Decision
Run a one-shot, cron-driven poller (pm-comms poll-inbox) over Graph. It keeps a per-mailbox high-water mark in .pm/.comms-state/last-poll.json (receivedDateTime gt last) so each run only sees new mail, and records every ingested Graph message id on the ticket as an idempotency guard so a re-poll is a no-op. Threading is resolved in a single scan two ways: by Graph conversationId (requester replies stay in-thread) OR by a [T-NNNN] tag injected into the subject (so a reply that starts a new conversation still lands on the right ticket). Bodies are run through a conservative plain-text cleaner that strips quoted reply chains and signatures (Gmail/Apple/Outlook markers). Senders are gated by an allow-list and an ignore-list.

## Consequences
No public webhook endpoint, no renewable subscriptions, and no always-on listener — just a stateless cron tick over the existing Graph auth, with at-most-once ticket creation guaranteed by recorded message ids. Trade-offs: inbound latency is bounded by the cron interval (not instant), correctness depends on persisted poller state (a lost last-poll.json re-scans the lookback window), and the [T-NNNN] subject tag becomes load-bearing for cross-conversation threading.