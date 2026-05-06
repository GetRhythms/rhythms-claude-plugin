---
name: rhythms-guide
description: Core behavioral guidance for working with Rhythms. Use whenever Rhythms MCP tools are involved so responses follow the current Rhythms conventions for discovery, identity, ambiguity handling, markdown links, and safe mutations.
---

# Rhythms Guide

Use this skill whenever you are working with Rhythms data or tools.

## Core principles

- Prefer explicit user intent over assumptions.
- Use the most direct tool that fits the user's goal.
- Base conclusions only on tool output.
- Ask for clarification when a supplied name could refer to more than one record.

## Identity rule

Use `whoami` only when identity or tenant context is required to answer correctly.

Examples:

- "Who am I in Rhythms?"
- "Show my goals"
- "What tenant am I working in?"

Do not call `whoami` by default on every Rhythms request.

## Discovery vs exact-resource rule

Use discovery-style tools when the user is still asking what exists.

Examples:

- use `search_knowledge` when the user is exploring goals, documents, layouts, or playbooks
- use `search_views` when the user is looking for a saved view

Once the target is known, switch to the direct domain tool.

Examples:

- `get_goal`
- `get_document`
- `read_document_content`
- `get_view_data`

Do not stay in broad search mode once the user has identified the exact resource they want.

## Link rendering rule

When Rhythms records contain enough data to construct application URLs, render them as markdown links with human-readable text.

- goals: use the goal title as link text
- documents and layouts: use the document name as link text
- views: use the view name as link text

Do not paste raw URLs inline when you can render a normal markdown link.

## User-facing language

- Do not expose UUIDs or internal identifiers unless the user explicitly asks for them.
- Do not narrate tool plumbing.
- Speak in terms of the user's goals, documents, views, teams, and time periods.

## Safe mutation rules

Before mutating Rhythms data:

- make sure the target record is clear
- confirm destructive operations explicitly
- confirm important payload fields for check-ins and content edits

For ambiguous targets:

- stop and ask which record the user means
- do not guess based on a partial name

## Document editing rule

For Rhythms document content:

- read current content before editing
- treat the body as Tiptap JSON
- prefer the smallest correct change instead of replacing the full document casually

## Recommended patterns

### Searching for content

1. Use the relevant discovery tool.
2. Present concise results with markdown links.
3. Ask a clarifying question if more than one plausible match exists.

### Updating a goal

1. Resolve the exact goal first.
2. Confirm the change.
3. Call the appropriate mutation tool.
4. Summarize the result without inventing extra details.

### Editing a document

1. Resolve the target document.
2. Read the current content.
3. Apply the smallest correct update.
4. Summarize what changed.

## Constraints

- Never infer missing permissions, statuses, or relationships.
- Never fabricate Rhythms links.
- Never mutate an ambiguous target.
