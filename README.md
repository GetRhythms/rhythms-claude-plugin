# Rhythms Claude Marketplace

This repository hosts the public Claude Code marketplace for Rhythms.

It distributes the `rhythms` plugin, which connects Claude Code to the Rhythms usprod MCP server at `https://mcpservers.rhythms.ai/rhythms/mcp`.

## Add the marketplace

```bash
claude plugin marketplace add GetRhythms/rhythms-claude-plugin
```

## Install the plugin

```bash
claude plugin install rhythms@rhythms-marketplace
```

## Verify installation

Inside Claude Code:

```text
/mcp
```

You should see a plugin-provided server named `rhythms`.

## Notes

This repository is a generated marketplace artifact published from Rhythms' private source repository. Direct edits here may be overwritten by the release pipeline.
