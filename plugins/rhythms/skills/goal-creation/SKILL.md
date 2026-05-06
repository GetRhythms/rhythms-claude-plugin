---
name: goal-creation
description: Create Rhythms goals through a guided workflow. Use when the user wants to draft objectives, key results, or initiatives, create goals for a team and time period, or turn a planning idea into structured Rhythms goals.
---

# Rhythms Goal Creation

Use this skill when the user wants to create goals in Rhythms.

## Primary tools

Use the current goal-creation and reference tools:

- `whoami` when identity matters
- `list_teams`
- `get_time_periods`
- `get_settings` when OKR model or terminology guidance matters
- `create_goals`

## Goal

Create the smallest correct set of goals after the user has reviewed the proposal.

## Workflow

### 1. Gather the minimum necessary context

Before creating goals, make sure you know:

- the intended team or teams
- the intended time period
- whether the user wants objectives, key results, initiatives, or a mix

Use `list_teams` and `get_time_periods` when the user gives names rather than uuids.

Use `get_settings` if the workspace's OKR model or terminology could affect how you should structure the proposal.

### 2. Draft before creating

Do not call `create_goals` immediately.

First show the proposed structure in plain language:

- the goal titles
- the goal types
- the intended team or owner context
- the chosen time period

If the user wants a hierarchy, make that explicit before creation.

### 3. Confirm the plan

Before calling `create_goals`, confirm:

- which goals will be created
- the time period
- any owners, teams, delegates, labels, parents, or metrics that matter

### 4. Create the goals

Use `create_goals` only after approval.

Prefer the simplest payload that satisfies the user's request. Do not over-specify fields that the user has not asked for.

## Creation guidance

- Use `objective` by default unless the user clearly wants a key result or initiative.
- If a time period is required and the user did not specify one, resolve it before creation.
- If the user asks for metrics, include them only when you can do so accurately.
- If parents are needed, make sure the hierarchy is explicit and deliberate.

## Constraints

- Do not create goals without review and confirmation.
- Do not guess the time period.
- Do not invent owners, labels, or metrics.
- Keep the first proposal concise and easy to approve or edit.
