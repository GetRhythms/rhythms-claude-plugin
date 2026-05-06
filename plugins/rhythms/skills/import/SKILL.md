---
name: import
description: Turn pasted planning content or structured source material into Rhythms goals. Use when the user wants to import goals from a document, spreadsheet, notes, or pasted text instead of creating each goal manually.
---

# Rhythms Import

Use this skill when the user gives source material and wants it converted into Rhythms goals.

## Primary tools

Use:

- `list_teams`
- `get_time_periods`
- `list_users` when owner resolution is needed
- `create_goals`

## Goal

Transform source material into a clean, reviewable creation proposal, then create the approved goals.

## Supported source styles

This skill is best for:

- pasted planning notes
- bullet lists of goals
- copied content from a document
- spreadsheet-like structured text

If the source is an actual Rhythms document that should first be reviewed in place, read it using the document tools before extracting content from it.

## Workflow

### 1. Parse the source

Extract the likely:

- titles
- goal types
- owner hints
- team hints
- time period hints
- metric hints

Do not assume every line is a goal. Group and interpret carefully.

### 2. Resolve required references

Before creation, resolve or clarify:

- teams
- time periods
- owners if they matter

Use current lookup tools instead of inventing identifiers.

### 3. Show the proposal first

Summarize what you plan to create before creating anything.

Good examples:

- "I found 3 objectives and 6 likely key results."
- "I mapped these items to the Product team for Q3."
- "I still need you to confirm whether these should be initiatives or objectives."

### 4. Create only after approval

Use `create_goals` once the structure is approved.

## Constraints

- Do not import blindly.
- Do not guess unresolved teams, owners, or time periods.
- Do not treat every source as a perfect one-to-one map to Rhythms goals.
- Prefer a smaller correct import over a larger speculative one.
