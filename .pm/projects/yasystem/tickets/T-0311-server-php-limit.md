---
id: T-0311
title: Server PHP Limit
type: support
state: in_progress
priority: p0
created: 2026-06-09T12:18:07Z
updated: 2026-06-18T18:25:00Z
project: yasystem
section: null
parent: null
children: []
order: 5120
reporter:
  kind: customer
  name: Austin Pickering
  channel: email
  contact: austin@yahire.com
assignee:
  kind: human
  name: Austin Pickering
acceptance_criteria:
  - Check the server memory limit. Increase if possible
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - inbound-email
attention: null
customer_impact: Check the memory limit on yasystem main server, ben can not run more than 6mo on the revenue splitter.
intake_channel: email
intake_mailbox: support@yahire.com
intake_message_id: AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAAAAAAEMAACnphZ2BstsS50BbU5pWRaYAAdLlziRAAA=
intake_message_ids:
  - AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAAAAAAEMAACnphZ2BstsS50BbU5pWRaYAAdLlziRAAA=
conversation_id: AAQkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgAQAJfQCcb1IsdBpnf3hH5Vc4M=
version: 7
---

## Request

**From:** Austin Pickering <austin@yahire.com>  
**Subject:** Server PHP Limit  
**Received:** 2026-06-09T12:18:07Z

## Conversation

**2026-06-09 12:18 — Austin Pickering <austin@yahire.com>**

Check the memory limit on yasystem main server, ben can not run more than 6mo on the revenue splitter.

---

**2026-06-18 18:14 — Austin Pickering**

**Fatal error**: Allowed memory size of 536870912 bytes exhausted (tried to allocate 53248 bytes) in **/var/www/yasystem/vendor/yiisoft/yii2/web/ErrorHandler.php** on line **266**

**2026-06-18 18:22 claude-code:** **Root cause found (diagnostic run on the live Ya System server, read-only — no changes made):**

The Revenue Splitter crashes once the date range gets large (Ben's "more than 6 months") because the web server caps how much memory a single page can use at **512 MB**, and the report needs more than that for a 6‑month window.

Two parts to this:

1. **The cap is being forced at the server level.** The PHP web config (`/etc/php/8.3/fpm/pool.d/www.conf`) has a hard line `php_admin_value[memory_limit] = 512M`. The Revenue Splitter code actually *tries* to lift this limit for itself, but a "php_admin_value" can't be overridden by the application — so the app's request is silently ignored and it dies at exactly 512 MB. This is why bumping things in code alone has had no effect.

2. **The report is memory-hungry by design.** It loads every quote and contract in the date range fully into memory at once (with related customer/address/rating data attached). Memory use grows roughly in line with the length of the window, which is why ~6 months is the tipping point.

**Good news on capacity:** the server has 15 GB RAM with ~14 GB free, so there's plenty of headroom to raise the limit safely. (Note: no swap configured, so we'll pick a sensible ceiling rather than "unlimited".)

**Proposed fix — two stages:**
- *Immediate (unblocks Ben today):* raise the web memory cap so the heavy admin reports get the headroom they need. Pending Austin's go-ahead on the exact value, since it's a production config change.
- *Durable:* change the Revenue Splitter to summarise in the database / process in chunks instead of loading everything into memory, so it stops needing a huge limit at all.

Awaiting decision on the immediate value before making any server change.

**2026-06-18 18:25 claude-code:** **Immediate fix applied to the live Ya System server (approved by Austin).**

Raised the web page memory cap from **512 MB → 2 GB**:
- File: `/etc/php/8.3/fpm/pool.d/www.conf`, line 67 — `php_admin_value[memory_limit]` changed `512M` → `2048M`.
- Backed up the original first (`www.conf.bak.t0311`).
- Config test passed, PHP-FPM reloaded cleanly, service confirmed active.

This is a hard ceiling for every page (the server has 15 GB RAM with ~14 GB free, so 2 GB is comfortably safe, and capping it — rather than going unlimited — protects the box since it has no swap).

**Next step for Ben:** please re-run the Revenue Splitter over the 6‑month range that was failing and confirm it now completes. If a much longer range (e.g. a full year) still struggles, that points to the durable fix below rather than another bump.

**Durable fix (still to do):** the report loads every quote and contract in the range fully into memory at once. Recommended follow-up is to rework it to aggregate in the database / process in batches so it no longer needs a large memory limit at all. Can be scheduled as a separate piece of work.
