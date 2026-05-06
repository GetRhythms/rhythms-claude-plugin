---
name: knowledge-search
description: Discover Rhythms content before taking action. Use when the user wants to search across Rhythms knowledge, find a document or playbook, locate planning content, or explore what exists before reading or editing a specific item.
---

# Rhythms Knowledge Search

Use this skill when the user is still in discovery mode and needs to find the right Rhythms content first.

## Primary tool

Use `search_knowledge` for discovery-oriented questions.

Examples:

- finding planning playbooks
- locating documents by topic
- exploring content related to a team or initiative
- searching across goals, documents, layouts, and playbooks

## Search-first behavior

When the user is asking "what exists?" or "where is ...?", start with `search_knowledge` rather than jumping directly into a write flow.

Once the correct target is found:

- switch to the more specific domain tool for reads or edits
- do not keep using `search_knowledge` when a direct tool is now a better fit

## Identity rule

Only use `whoami` when identity or tenant context is actually needed to answer the question correctly.

Do not call `whoami` by default.

## Link rendering rule

When Rhythms records include enough information to construct application links, render them as markdown links with human-readable text.

Use the record's title or name as the link text.

Do not paste raw URLs inline when a proper markdown link can be constructed from the returned data.

## Constraints

- Prefer direct user intent over assumptions.
- If a search result could refer to multiple records the user might mean, ask a clarifying question before mutating anything.
- Base conclusions only on tool output, not on guesses.
