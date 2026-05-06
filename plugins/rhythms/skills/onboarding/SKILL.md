---
name: onboarding
description: Orient a user to their Rhythms workspace and suggest sensible next steps. Use when the user is new to Rhythms, asks to get started, wants an overview of their workspace, or uses `/rhythms:setup` as their first meaningful Rhythms workflow.
---

# Rhythms Onboarding

Use this skill to help a user understand what already exists in their Rhythms workspace and what they should do next.

## Primary tools

Use the current curated tools:

- `whoami`
- `list_teams`
- `get_time_periods`
- `search_knowledge`
- `get_settings`

## Goal

Answer these questions in order:

1. Who is the current user and what tenant are they in?
2. What teams and time periods already exist?
3. Does the workspace appear to have meaningful goals or document content already?
4. What is the most helpful next step for this user?

## Suggested workflow

### 1. Identify the current user

Use `whoami` when onboarding or workspace orientation depends on knowing the current user and tenant.

Do not surface technical tenant details unless they help the user.

### 2. Inspect the workspace

Use:

- `list_teams` to see whether the workspace has teams
- `get_time_periods` to see whether planning cycles exist
- `search_knowledge` to see whether goals, documents, layouts, or playbooks already exist

If the user explicitly asks how Rhythms is configured, also use `get_settings`.

### 3. Classify the situation

Use the results to infer one of these broad situations:

- mostly empty workspace
- active workspace with existing teams and content
- partially configured workspace with some structure but little content

Do not overstate certainty. If the signals are mixed, say that the workspace appears partially configured.

### 4. Respond with orientation, not plumbing

Give the user a concise summary such as:

- what teams exist
- what time periods exist
- whether meaningful goals or documents already exist
- what they can do next

Good next steps might be:

- search for existing goals or planning docs
- create goals for a team and time period
- review or update a document
- invite or organize people if teams are missing

## If the workspace looks empty

Suggest the smallest next useful action instead of giving a giant tutorial.

Good examples:

- create a team structure
- confirm the current planning period
- create the first goals
- import goals from a planning document

## If the workspace already has content

Prefer showing the user the most relevant existing things first:

- teams they likely belong to
- active time periods
- related goals or documents

Then offer the next action.

## Constraints

- Do not expose UUIDs.
- Do not claim that the org is brand new unless the evidence is genuinely strong.
- Do not create teams, goals, or documents without confirmation.
