---
id: T-0334
title: adding document uploads to meetings
type: feature
state: done
priority: p2
created: 2026-06-10T00:01:59Z
updated: 2026-07-02T18:10:22Z
project: pm-tool-self
section: null
parent: null
children: []
order: 96256
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] A meeting can have documents/files attached, the same way tickets already support attachments"
  - "[x] Attached documents are listed on the meeting and can be downloaded"
  - "[x] Uploads reuse the existing ticket-attachment storage/mechanism rather than a new one"
out_of_scope: []
code_anchors:
  - path: web/app/_actions/meetings.ts
    symbol: upload/get/removeMeetingAttachment
  - path: web/app/_components/AttachmentsPanel.tsx
    symbol: shared AttachmentsPanel
  - path: web/app/_components/MeetingAttachments.tsx
    symbol: MeetingAttachments wrapper
  - path: schemas/meeting.schema.json
    symbol: $defs/attachment (file shape)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260702-1250
    started: 2026-07-02T12:50:51Z
    status: completed
    ended: 2026-07-02T12:51:05Z
    summary: "You can now attach documents and images to a meeting, just like you already can on a ticket. Before, a meeting had nowhere to keep its agenda doc, slide deck, or a photo of the whiteboard — those had to live elsewhere and get lost. The meeting page now has an Attachments card where you upload files; each is listed with its size and who added it, downloads on click, and images open in the same zoom/pan viewer tickets use (handy for big diagrams). It reuses the exact storage tickets already use (files in S3, only the file details recorded on the meeting) rather than inventing a new one — so nothing new to configure. Works on project meetings, org-wide meetings, and pre-project meetings. If we'd left it, meeting materials would keep scattering across inboxes and drives. Benefit: a meeting's documents live with the meeting."
    test_plan: "1. Open a meeting detail page (project or org-wide). Find the \"Attachments\" card below Notes. 2. Click Upload, pick a PDF → it appears in the list with size + your email, and persists after refresh. 3. Click the file → it downloads / opens via a short-lived link. 4. Upload an image → clicking it opens the full-screen zoom/pan viewer (wheel + buttons zoom); close returns to the page. 5. Remove (×) a file → it disappears and the S3 object is deleted. 6. Confirm tickets' own attachments still work unchanged (same shared panel/viewer) — upload, view image, download, remove on a ticket. 7. Edge: a >25 MB file is rejected with a clear message; with no S3 bucket configured, upload returns \"storage isn't set up\" rather than erroring. 8. Repeat the happy path on a pre-project meeting (org bucket). Note: attachment storage needs the S3 bucket configured on the live host."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 13
collaborators:
  - kind: human
    name: Austin Pickering
---

same as with tickets

## Conversation

**2026-07-02 12:51 claude-code:** Run run-20260702-1250 completed — You can now attach documents and images to a meeting, just like you already can on a ticket. Before, a meeting had nowhere to keep its agenda doc, slide deck, or a photo of the whiteboard — those had to live elsewhere and get lost. The meeting page now has an Attachments card where you upload files; each is listed with its size and who added it, downloads on click, and images open in the same zoom/pan viewer tickets use (handy for big diagrams). It reuses the exact storage tickets already use (files in S3, only the file details recorded on the meeting) rather than inventing a new one — so nothing new to configure. Works on project meetings, org-wide meetings, and pre-project meetings. If we'd left it, meeting materials would keep scattering across inboxes and drives. Benefit: a meeting's documents live with the meeting.

---

**2026-07-02 18:10 — you**

All done and tested
