# Rhythms Claude Plugin

This repository contains the published Claude Code plugin for Rhythms.

The plugin connects Claude Code to the Rhythms usprod MCP server at `https://mcpservers.rhythms.ai/rhythms/mcp` so Claude can work with goals, documents, layouts, views, teams, users, reference data, organization settings, and workspace workflows.

## What is included

- A Claude Code plugin manifest in `.claude-plugin/plugin.json`
- A root `.mcp.json` for the Rhythms MCP server connection
- Workflow skills for setup, onboarding, search, goal creation, goal management, goal check-ins, document editing, knowledge search, and Rhythms-specific guidance
- Command entry points for common search, goal creation, check-in, and goal-management flows

## Install locally from a checkout

If you have this marketplace repository checked out locally, run:

```bash
claude --plugin-dir /absolute/path/to/this/repository/plugins/rhythms
```

Then inside Claude Code:

```text
/reload-plugins
```

```text
/mcp
```

You should see a plugin-provided server named `rhythms`.

## Smoke test flow

1. Confirm the plugin loads and `/mcp` shows `rhythms`.
2. Complete any required authentication flow for the Rhythms MCP server.
3. Try a safe read-only call such as `whoami`, `get_time_periods`, or `search_knowledge`.
4. Then try one workflow skill such as `/rhythms:setup` or `/rhythms:knowledge-search`.

## Commands included

- `/rhythms:search`
- `/rhythms:create-goals`
- `/rhythms:checkin`
- `/rhythms:manage-goals`

## Skills included

- `/rhythms:rhythms-guide`
- `/rhythms:setup`
- `/rhythms:onboarding`
- `/rhythms:goal-creation`
- `/rhythms:goal-management`
- `/rhythms:goal-checkin`
- `/rhythms:document-editing`
- `/rhythms:knowledge-search`
- `/rhythms:rhythms-intelligence`

## Notes

This published plugin artifact is generated from Rhythms' private source repository. Direct edits here may be overwritten by the publish pipeline.
