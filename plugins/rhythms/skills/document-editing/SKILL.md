---
name: document-editing
description: Read and edit Rhythms documents or layouts safely. Use when the user wants to inspect document content, update a document, append or replace content, or work with a Rhythms layout or document body.
---

# Rhythms Document Editing

Use this skill for Rhythms document and layout content work.

## Primary tools

Use these tools in order as needed:

1. `get_document` to resolve the target document or layout
2. `read_document_content` to inspect the current body
3. `edit_document_content` to update the body

## Core rules

- Treat document bodies as Tiptap JSON, not freeform markdown or plain text.
- Read the current content before editing.
- Do not fabricate Tiptap structures that you have not verified.
- If the user refers to a document by name and the target could be ambiguous, ask them to clarify.

## Safe workflow

### 1. Resolve the document

Use `get_document` with:

- `document_uuid` when available
- otherwise `document_name` plus the correct `document_type`

### 2. Read before writing

Use `read_document_content` before `edit_document_content`.

This is required so you can:

- understand the current structure
- preserve surrounding content
- identify whether a targeted update is possible

### 3. Choose the smallest correct edit

By default, `edit_document_content` replaces the whole body.

If the current document content includes a known node hash or targetable section and the requested change is local, prefer a targeted operation such as:

- `replace`
- `insert_before`
- `insert_after`

If you do not have a stable target for a precise edit, do not pretend you do. Either:

- perform a full replacement carefully, or
- ask the user for confirmation before making a broader edit

## When creating a new document

If the user wants a new document with initial content:

1. create the document record first
2. then write the body content separately

Do not assume `create_document` also creates the body content.

## Constraints

- Do not skip the read step.
- Do not flatten rich content into plain text unless the user explicitly wants that.
- Do not overwrite the whole document casually when a section-level update is possible.
