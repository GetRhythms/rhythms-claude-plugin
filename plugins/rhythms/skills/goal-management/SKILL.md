---
name: goal-management
description: Update, share, or delete Rhythms goals safely. Use when the user wants to rename a goal, change owners or teams, update dates or visibility, share a goal, delegate ownership-related work, or remove a goal.
---

# Rhythms Goal Management

Use this skill when the user wants to change existing goals in Rhythms.

## Primary tools

Use the current curated goal tools:

- `get_goal`
- `update_goals`
- `share_goals`
- `delete_goals`

## Resolve the target first

Do not update or delete a goal until the target is unambiguous.

If the user provides:

- a goal UUID, use it
- only a goal title or shorthand description, resolve the exact goal first

If more than one plausible goal matches, ask the user to clarify before mutating anything.

## Updating goals

Use `update_goals` for changes such as:

- title
- description
- dates
- time period
- owners
- teams
- delegates
- labels
- parent relationships
- visibility
- metric settings

Before calling `update_goals`:

1. confirm which goal is being changed
2. confirm the intended fields to update
3. make sure the user understands any broad replacement effect if you are sending list-style fields

When the user says "add" rather than "replace" for multi-value fields like delegates, labels, teams, or owners, avoid overwriting the current state accidentally. Inspect the current goal first when needed.

## Sharing goals

Use `share_goals` when the user wants to give users or teams access to goals.

Before sharing:

1. confirm the target goals
2. confirm who the goal should be shared with
3. prefer users or teams that can be resolved reliably

If the target user or team is ambiguous, clarify first.

## Deleting goals

Use `delete_goals` only after explicit confirmation.

Deletion should be treated as destructive even when the user sounds confident.

Before deleting:

1. confirm exactly which goal or goals will be removed
2. confirm whether the user intends a permanent delete rather than some softer change like closing or updating status elsewhere

For multi-goal deletes, be especially explicit about scope.

## Response style

After a successful mutation:

- summarize what changed
- name the affected goal or goals
- do not invent fields that were not returned

After a failed mutation:

- explain the blocking issue
- ask only for the next missing piece of information

## Constraints

- Do not mutate an ambiguous goal.
- Do not treat "add" as "replace" for list-style fields without checking.
- Do not delete without explicit confirmation.
