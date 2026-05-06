---
name: insights
description: Analyze Rhythms workspace data and summarize what matters. Use when the user wants a progress summary, team-level view, saved-view analysis, or a quick understanding of how goals or planning artifacts are doing.
---

# Rhythms Insights

Use this skill when the user wants analysis rather than a single record lookup or a direct mutation.

## Primary tools

Prefer these current tools for insight-oriented workflows:

- `search_views`
- `get_view_data`
- `search_knowledge`
- `get_goal`
- `whoami` when the user's identity is relevant

## Goal

Provide concise, grounded analysis using real Rhythms data.

## Workflow

### 1. Choose the right source

Use `search_views` and `get_view_data` when:

- the user wants a report-style answer
- they refer to a saved view
- they want structured goal data or a report preview

Use `search_knowledge` when:

- the user is asking broad discovery questions
- they want to find planning documents, playbooks, or goal-related content

Use `get_goal` when:

- the user wants analysis on one known goal

### 2. Lead with the insight

Write the summary first.

Examples:

- what looks healthy
- what appears at risk
- what is missing
- which area likely needs attention

Then include supporting links or references to the relevant Rhythms entities.

### 3. Stay grounded

Only use trends or comparisons that the returned data supports.

If the available data is not enough for a real trend or comparison, say so plainly.

## Good use cases

- summarize a saved view
- compare a couple of teams or workstreams using a relevant view
- analyze one goal in detail
- locate planning artifacts relevant to a quarter or initiative

## Constraints

- Do not fabricate percentages or status changes.
- Do not claim a trend without enough evidence.
- Do not use discovery results as if they were a full analytical dataset when a saved view would be better.
