---
id: ADR-002
slug: adopt-milkdown-for-wysiwyg-body-editing
title: Adopt Milkdown for WYSIWYG body editing
state: accepted
decided: 2026-05-29
decided_by:
  - austin (human)
  - claude (agent)
project: pm-tool-self
supersedes: null
superseded_by: null
tickets: []
---

# ADR-002: Adopt Milkdown for WYSIWYG body editing

## Context

The PM (Austin) does not write markdown. Today every body editor is a raw `<textarea>`, which
pushes a developer-centric authoring model onto a non-developer user. The tool needs a real
WYSIWYG surface without abandoning the markdown-on-disk source of truth.

## Decision

Use **Milkdown** (`@milkdown/kit` + `@milkdown/react`, already installed and typechecking under
React 19) as a shared `RichBodyEditor` component. It edits rich text but **round-trips to a
markdown string**, persisted through the existing `writeMarkdown` server actions so the `.md`
files remain canonical. Add a **round-trip snapshot test** to bound diff drift (edit → serialize →
compare), so we catch any case where the editor mangles markdown on a no-op edit.

## Consequences

- The storage model is unchanged — files stay markdown, frontmatter preserved.
- The CLI, linter, and agents keep working against the same files with no changes.
- Risk is round-trip fidelity; the snapshot test is the mitigation and the regression guard.
