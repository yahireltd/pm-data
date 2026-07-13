---
id: T-0550
title: "Customer Profile: attach documents (Word strategic account plans) to a customer"
type: feature
state: triaged
created: 2026-07-13T10:30:36Z
updated: 2026-07-13T10:56:08Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: email (forwarded request)
assignee:
  kind: human
  name: zsolt@yahire.com
acceptance_criteria:
  - The customer profile page (ya-hire) has a "Documents" section where a staff member can upload a file against that customer.
  - Word documents are supported (.doc and .docx) as the primary case; PDF should also be accepted (confirm full allowed-type list with requester).
  - "Each uploaded document is listed with: file name, who uploaded it, date/time, and file size — with a download link."
  - A staff member can delete a document (with a confirmation prompt); deletion is recorded (who/when).
  - Uploaded files are stored so they are NOT publicly guessable/downloadable — only reachable by a logged-in staff member with access to that customer (served through a controller check, not a public path).
  - File type and size are validated on upload (reject unexpected types; enforce a max size — confirm limit); a clear error is shown on rejection.
  - The feature does not break or slow the existing customer profile page for customers with no documents.
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/SalesController.php
    note: customer profile action (view-customer-profile) — add upload/list/download/delete actions + access checks
  - path: ya-hire/backend/views/sales/view-customer-profile.php
    note: customer profile view — add the Documents section (upload form + list)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - customer-profile
  - documents
  - account-management
  - sales
attention: null
version: 4
collaborators:
  - kind: human
    name: Zsolt
---

## Problem
A sales / account manager asked (by email, forwarded by Zsolt) for a way to **attach documents to a Customer Profile** in yasystem. The main use case is **Word documents — strategic account plans** kept against the customer.

> "I want a section where you can attach documents. It will be for word documents, mostly strategic account plans."

## Context
- Lives on the **Customer Profile** page in the yasystem (ya-hire) backend — `SalesController` (`view-customer-profile`) + `backend/views/sales/view-customer-profile.php`.
- This fits the wider Sales Segmentation / Account Management direction (strategic account plans), but the change itself is a self-contained addition to the existing profile page, so it's filed under Yasystem where the code lives.
- Related pattern already on this page: the portal billing/detail change-request boxes render extra sections on the profile — the Documents section can follow a similar layout.

## Suggested scope (v1)
A simple, secure document store per customer: upload → list → download → delete. Keep it small and match the profile page's existing look.

## Open questions to confirm before building
1. **Storage location** — server disk (e.g. a non-public uploads dir served via a controller) vs cloud storage (S3/Azure). Any existing file-upload pattern in ya-hire to reuse?
2. **Who can upload / delete** — all sales staff, account owners only, or a specific role? Should delete be restricted?
3. **Allowed file types** — Word (.doc/.docx) confirmed; also PDF? Excel? Anything to explicitly block?
4. **Max file size** — a sensible cap (e.g. 10–25 MB)?
5. **Versioning / history** — is replacing a plan fine, or do they want to keep old versions?
6. **Retention / GDPR** — any rules on how long documents are kept, and are these ever customer-visible? (Assumption for v1: **staff-only**, never shown in the customer portal.)

## Notes
- Acceptance criteria below are a first draft — confirm the open questions with the requester before claiming (Definition of Ready).
- Keep the file store **not publicly guessable** and gate downloads behind a staff login + access check.

## Conversation

**2026-07-13 10:32 — Zsolt**

Next to the addresses section, under the notes is space for this
