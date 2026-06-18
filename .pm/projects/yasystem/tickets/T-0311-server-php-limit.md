---
id: T-0311
title: Server PHP Limit
type: support
state: in_progress
priority: p0
created: 2026-06-09T12:18:07Z
updated: 2026-06-18T18:14:15Z
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
version: 5
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
