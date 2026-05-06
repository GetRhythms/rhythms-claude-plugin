---
name: goal-checkin
description: Create a Rhythms goal check-in safely and with the right confirmations. Use when the user wants to record progress on a goal, update a check-in value, or add a status or note to a goal in Rhythms.
---

# Rhythms Goal Check-In

Use this skill when the user wants to create a check-in in Rhythms.

## Primary tool

Use `create_checkin`.

## Required confirmation before creating the check-in

Before you call `create_checkin`, confirm the relevant fields with the user:

- `goal_uuid`
- `value` if applicable
- `status` if applicable
- `score` if applicable
- `note` if applicable
- `checkin_date` if applicable

Do not create the check-in until the intended values are clear.

## Important behavior

- `goal_uuid` is required for a reliable check-in flow.
- If the user only gives a goal title or shorthand description, do not guess.
- If the user cannot provide the uuid directly, help them discover the correct goal first using the available read tools or other context they already have.
- If there is any ambiguity about which goal to update, stop and ask for clarification.

## Suggested workflow

1. Confirm which goal the user means.
2. Confirm the actual check-in payload.
3. Call `create_checkin`.
4. Summarize the result clearly.

## Response style

When the check-in succeeds:

- mention the goal that was updated
- mention the fields that were recorded
- avoid inventing details not returned by the tool

When the check-in cannot proceed:

- explain what is missing, usually `goal_uuid` or one of the intended values
- ask only for the next piece of information needed

## Constraints

- Never guess the target goal.
- Never create the check-in before confirmation.
- Keep the workflow one goal at a time unless the user explicitly asks for multiple check-ins.
