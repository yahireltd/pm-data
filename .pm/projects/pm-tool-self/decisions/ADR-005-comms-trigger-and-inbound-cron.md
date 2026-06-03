---
id: ADR-005
slug: comms-trigger-and-inbound-cron
title: Comms triggers via server-action hooks; inbound email via opt-in cron
state: accepted
decided: 2026-04-26
decided_by:
  - austin
project: pm-tool-self
supersedes: null
superseded_by: null
tickets: []
kind: dependency
---

# ADR-005: Comms trigger model and inbound email loop

## Context

Two questions, related:
1. **Outbound:** when does pm-tool send a notification? Options: hook into every server action, run a cron that diffs current state against a "last notified" snapshot, or react to git commits.
2. **Inbound:** how does pm-tool see new emails at `salessupport@yahire.com`? Options: webhook (Microsoft Graph subscription), polling, or no automation at all (manual paste).

## Decision

**Outbound: server-action hooks.** Every mutating server action (`setTicketState`, `addComment`, `claimTicket`, `setAttention`, `createMeeting`, etc.) calls a single `dispatch(event)` function before returning. The dispatcher reads the entity's `stakeholders[]`, filters by `notify_on`, and queues a notification per matching stakeholder. Queue is durable (file-backed at `.pm/.comms-queue/`), drained by a separate worker (cron or daemon).

**Inbound: opt-in cron poll.** ya-hire's existing Outlook integration uses polling (no webhooks). Reuse the pattern: a console command `pm-comms poll-inbox` runs every 2 minutes via cron (or systemd timer). Reads `.pm/config.yml`'s `inbound_email` block to know which mailbox(es) to scan and which folder. Creates inbox tickets for new threads.

Configuration in `.pm/config.yml`:
```yaml
inbound_email:
  enabled: false                            # opt-in; ships disabled
  mailboxes:
    - address: salessupport@yahire.com
      folder: Inbox
      ticket_type: support                  # default type for created tickets
  poll_interval_seconds: 120
  ignore_senders:                           # auto-resolved bounce addresses
    - noreply@*
    - mailer-daemon@*
```

## Consequences

**Positive:**
- Hook model is simple to reason about — one place to change notification logic, every action goes through it.
- Durable queue means notifications survive process restarts.
- Cron polling matches how ya-hire already does it; no new auth or subscription complexity.
- Opt-in default means a fresh pm-tool install doesn't accidentally start hammering Microsoft Graph.

**Negative:**
- Hook in every server action = boilerplate. Mitigation: the action wrapper handles it generically; individual actions just emit `event.kind`.
- Polling has up to 2-minute latency on inbound. Acceptable for support email; would not be for chat. We'd add webhook support later if needed.
- Queue file under git could be noisy. Mitigation: `.pm/.comms-queue/` is gitignored; queue state is local to the worker.

## Implementation skeleton

```
comms/
  dispatch.ts          # the central dispatch(event) function
  channels/
    email.ts           # outbound — uses Microsoft Graph (stubbed pre-creds)
    log.ts             # writes to .pm/.comms-log/ regardless (audit + dev)
  templates/
    state-change.md    # markdown templates with {{ var }} interpolation
    comment-added.md
    meeting-scheduled.md
    ...
  inbound/
    graph-poll.ts      # the cron entry; reads config, calls Graph, creates tickets
    parser.ts          # email → ticket mapper (reporter, body, attachments)
  queue/
    enqueue.ts
    drain.ts
```

## Alternatives considered

- **Webhooks via Graph subscription.** Cleaner real-time but requires a public HTTPS endpoint, expiring subscriptions to renew, validation handshakes. Defer until polling is proven inadequate.
- **Reactive via git post-commit hook.** Cute but couples notifications to a git workflow that not all users follow. Rejected.
- **Per-action explicit notify calls.** Goes against the "one place to change notification logic" goal. Rejected.
