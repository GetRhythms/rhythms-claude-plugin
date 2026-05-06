---
name: scheduled-tasks
description: Help the user design recurring Rhythms workflows using Claude scheduled tasks. Use when the user wants reminders, recurring summaries, periodic check-in prompts, or repeatable Rhythms analysis on a schedule.
---

# Rhythms Scheduled Tasks

Use this skill when the user wants recurring Rhythms help, not just a one-time answer.

## Goal

Translate the user's recurring need into a clear scheduled-task prompt that can run Rhythms workflows safely.

## Good recurring workflows

- weekly goal check-in reminders
- recurring summaries of saved views
- periodic scans for goals that need attention
- regular planning document review prompts

## Workflow

### 1. Clarify the recurring job

Confirm:

- what should happen
- how often it should happen
- what scope it should use, such as team, time period, goal, or saved view

### 2. Prefer read-heavy automations

Scheduled tasks are best for:

- reminders
- summaries
- discovery
- draft suggestions

Be conservative about write actions. If the workflow would change Rhythms data, make sure the user explicitly wants that behavior and understands the impact.

### 3. Write a clear scheduled-task prompt

Describe the task in plain language using current Rhythms workflows.

Examples:

- search for relevant goals and summarize what needs attention
- load a saved view and summarize changes
- find planning documents related to a quarter and remind the user to review them

## Constraints

- Do not assume the user wants automatic mutations.
- Default to drafts, summaries, and reminders rather than unattended writes.
- Confirm cadence before suggesting the final scheduled prompt.
