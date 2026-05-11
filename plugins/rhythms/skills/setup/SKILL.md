---
name: setup
description: Verify the Rhythms plugin is loaded and troubleshoot hosted Rhythms MCP connection issues. Use when the user wants to test the plugin against its configured Rhythms MCP server, confirm the Rhythms MCP server is visible, or debug missing Rhythms tools after installing or reloading the plugin.
---

# Rhythms Plugin Setup

Use this skill to verify that the Rhythms Claude plugin is connected correctly to its configured hosted Rhythms MCP environment.

## Goal

Confirm three things in order:

1. The plugin is loaded.
2. The Rhythms MCP server is visible in Claude Code.
3. The hosted MCP endpoint is reachable and ready for authenticated tool calls.

## Hosted endpoint

The plugin package is wired through `.mcp.json`. Treat that file as the source of truth for the hosted MCP endpoint.

## Setup flow

### 1. Confirm the plugin is loaded

- Ask the user to run `/reload-plugins` if they have just changed the plugin files.
- Then ask them to inspect `/mcp`.

Expected result:

- A plugin-provided MCP server named `rhythms` is present.

### 2. If `rhythms` is missing from `/mcp`

Guide the user through these checks:

- Confirm they launched Claude Code with `--plugin-dir` pointing at this plugin folder.
- Confirm the plugin root contains:
  - `.claude-plugin/plugin.json`
  - `.mcp.json`
  - `skills/`
- Ask them to run `claude plugin validate .` from the plugin directory.

Do not assume the plugin is loaded until `/mcp` shows `rhythms`.

### 3. If `rhythms` appears but is disconnected or unavailable

Treat this as a server reachability or auth problem.

Guide the user to verify:

- the hosted server in `.mcp.json` is the intended environment for this test
- the configured endpoint in `.mcp.json` matches the environment the user expects
- Rhythms MCP authentication prerequisites are configured for that hosted environment

### 4. If the server is connected

Recommend one read-only smoke test.

Prefer one of these:

- `whoami` when identity matters and authentication is expected to be working
- `get_time_periods` for a safer generic read
- `search_knowledge` for discovery-oriented testing

### 5. Report status accurately

- If `/mcp` does not show `rhythms`, say the plugin is not loaded yet.
- If `rhythms` is present but tools fail, say the plugin is loaded but the hosted MCP connection or auth still needs work.
- Only say the setup is working once the user has both:
  - a connected `rhythms` MCP entry in `/mcp`
  - at least one successful read-only tool call

## Constraints

- Do not jump straight to write tools during setup.
- Prefer read-only verification first.
- Do not infer authentication state without a successful tool call.
