---
id: T-0261
title: Photo / Document uploader / viewer needs to go to aws s3
type: feature
state: review
priority: p2
created: 2026-06-05T22:17:11Z
updated: 2026-06-08T15:07:38Z
project: pm-tool-self
section: null
parent: null
children: []
order: 83968
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - we can upload and view documents of all types
  - we can download / view these docs - images in particular need advanced zoom as ther are some big flow diagrams
out_of_scope: []
code_anchors:
  - path: comms/src/lib/s3.ts
  - path: web/app/_actions/tickets.ts
    symbol: uploadAttachment
  - path: web/app/_components/TicketAttachments.tsx
  - path: schemas/ticket.schema.json
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1507
    model: claude-opus-4-8
    started: 2026-06-08T15:07:03Z
    status: needs_review
    ended: 2026-06-08T15:07:38Z
    summary: "Tickets can now carry file attachments — documents of any type and images — stored in Amazon S3 rather than crammed into the project data. On a ticket you can upload a file, see the list of what's attached, download documents, and open images in a full-screen viewer that zooms and pans (so the big flow diagrams are actually readable). The files live in cloud storage and the app hands out a short-lived private link each time you open one; nothing sensitive is exposed permanently. We deliberately built this to use the server's own cloud permissions (its instance role) so there are no passwords or keys stored anywhere — matching how the tool already reads its other secrets. IMPORTANT: this is finished in code but has NOT been tested against real storage yet, because it needs cloud setup that only you can do — an S3 bucket, the server's role granted permission to read/write that bucket, and the bucket name set in the server's environment. Until that's in place the feature stays safely switched off (it simply says attachments aren't set up). Once the bucket and permission exist, it should work end to end and needs a real-world check. Marked needs-review for exactly that reason."
    test_plan: |-
      ## Provision first (Austin — required, can't be done from code)
      1. Create an S3 bucket (private, not public).
      2. Grant the server's IAM instance role: `s3:PutObject`, `s3:GetObject`, `s3:DeleteObject` on that bucket (it should already have Secrets Manager via the same role).
      3. Set `PM_S3_BUCKET=<bucket-name>` (and `AWS_REGION`) in the server env; restart.

      ## Then verify on a deployed ticket
      1. Open a ticket → the "Files" section shows an Upload button.
      2. Upload a PDF → it appears in the list with size; click it → downloads/opens via a presigned link.
      3. Upload a large image (e.g. a flow diagram) → click it → the zoom viewer opens; +/- buttons and mouse-wheel zoom work, the image pans by scrolling, Reset returns to 100%, Close dismisses.
      4. Remove an attachment via ✕ → it disappears and the S3 object is deleted.
      5. As a stakeholder (read-only) → can view/download but sees no Upload/Remove.
      6. Files over 25 MB are refused with a clear message.

      ## Before provisioning (already true now)
      - With no PM_S3_BUCKET set, the Files section shows "No files attached" and Upload returns "Attachments storage isn't set up yet" — the rest of the ticket page works normally.

      Notes: bundling verified (`next build` compiles; the aws-CLI lib stays server-side, not in the browser). ADR-036 records the design. Uploads route through the Next server (25 MB cap).
labels: []
attention:
  needed_by: human
  reason: Agent finished — needs review
  since: 2026-06-08T15:07:38Z
---

## Problem

_What's wrong / what's needed?_

## Context

## Design notes
