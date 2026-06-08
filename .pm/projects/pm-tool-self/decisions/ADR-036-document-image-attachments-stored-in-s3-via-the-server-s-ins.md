---
id: ADR-036
slug: document-image-attachments-stored-in-s3-via-the-server-s-ins
title: Document/image attachments stored in S3 via the server's instance role, using the aws CLI (no new SDK dependency)
state: accepted
decided: 2026-06-08
decided_by:
  - austin
  - claude
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0261
kind: integration
---

## Context

Tickets need real file attachments (T-0261) — documents of any type, plus images (big flow diagrams) that need zoom. The data lives in a shared filesystem repo; storing large binaries there is wrong (bloats git, no streaming). Austin's direction: the server runs under an IAM role granting S3 + Secrets Manager by default, so no credentials are stored anywhere.

## Decision

Store attachment **bytes in S3**; store attachment **metadata** (key, filename, content-type, size, uploadedBy, uploadedAt) in the ticket frontmatter as `attachments[]`.

Talk to S3 the same way we already talk to Secrets Manager: **shell out to the `aws` CLI** (`comms/src/lib/secrets.ts` pattern), which uses the machine's default credential chain — i.e. the instance role. This deliberately avoids adding an `@aws-sdk/*` npm dependency (the web app currently has none) and reuses the exact auth mechanism already deployed.

- **Upload:** browser → Next server action (FormData/File) → temp file → `aws s3api put-object` → append metadata. Server-side upload (not presigned PUT) because the CLI presigns GET only.
- **View/download:** `aws s3 presign s3://bucket/key --expires-in N` → short-lived GET URL handed to the client. Images open in a zoom/pan viewer; other types download.
- **Bucket name** from `PM_S3_BUCKET` env; region from `AWS_REGION`. Absent ⇒ the feature reports "storage not configured" and disables upload (graceful, like the secrets fallbacks).

## Failure modes & handling

- No bucket configured / no role / `aws` missing ⇒ actions return a clean "attachments storage isn't set up" error; the ticket page still works.
- Large files routed through the Next server: acceptable at this scale; a size cap is enforced server-side.
- Presigned URLs expire (short TTL) — the client fetches a fresh one per view.
- Key collisions avoided by prefixing keys with the ticket id + a random-ish suffix derived from filename+timestamp passed in (no Math.random in the data path).

## Consequences

- No new dependency; same IAM/credential story as the existing AWS usage.
- **Infra to provision (Austin):** an S3 bucket, the instance role granting `s3:PutObject/GetObject/DeleteObject` on it, and `PM_S3_BUCKET` in the server env. Until then the feature is inert-but-safe.
- Cannot be verified locally without the bucket/role — ships for review + live verification.