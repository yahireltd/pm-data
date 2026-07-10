---
id: T-0548
title: "PDF_Invoices storage: clean up generated PDFs instead of accumulating forever"
type: chore
state: triaged
created: 2026-07-10T22:40:08Z
updated: 2026-07-10T22:40:08Z
project: accounts-integrity
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee: null
acceptance_criteria:
  - "PDF_Invoices does not grow unbounded: files older than the agreed TTL are removed automatically"
  - The window.open URL flow and email-attachment flows still work (file survives long enough)
  - Inline-streamed PDFs (create-itemised-pdf) leave nothing behind, or are covered by the TTL sweep
  - Other generated-file directories checked and covered or explicitly excluded
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - storage
  - pdf
  - t-0538-follow-on
attention: null
version: 1
---

## Problem

Every invoice/credit-note PDF render writes a file to `backend/web/PDF_Invoices/` (both the legacy mpdf builder and the itemised Browsershot builder in `InvoicePdfService`) and **nothing ever deletes them**. With the T-0538 work adding PDF buttons everywhere (doc-integrity pages, manual-refunds/manual-payments statement rows), render volume goes up — the directory will grow unbounded on the live server.

## Constraints discovered during T-0538 (don't break these)

- **Two serving patterns coexist**: `create-itemised-pdf` streams the file inline via `sendFile` (could delete after send), but the proforma/JS flows (`create-pdf` + `window.open(frontendDestination)`) return a URL that the browser fetches in a SECOND request — the file must survive past the generating request.
- **Email flows attach the files**: the invoice-chase console job and contract email paths call `InvoicePdfService` and attach the written file — deletion must not race the attach.
- File names are per-document (`INV123-<contractRef>[-itemised].pdf`), so re-renders overwrite: growth is bounded by distinct documents rendered, not render count — but that's still every document ever viewed.

## Suggested approach

A scheduled cleanup (cron or on-demand) deleting files in `PDF_Invoices/` older than a short TTL (e.g. 24h — long enough for any browser fetch or email attach, short enough that storage stays flat), rather than delete-after-send (which breaks the URL-fetch pattern). Optionally: `create-itemised-pdf` writes to a temp name and unlinks after send, since it streams inline.

## Also check
Whether other generated-file directories have the same accumulation problem (e.g. brochures, statements, exports) — one sweep, one cleanup mechanism.