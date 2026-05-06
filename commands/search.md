---
description: Search Rhythms content and guide the user to the right resource.
---

Search Rhythms for the user's request. Follow the `knowledge-search` and `rhythms-guide` skills:

1. Start with `search_knowledge` or `search_views` when the user is in discovery mode.
2. If the user is asking about a specific resource after discovery, switch to the direct tool for that resource.
3. Present concise results using markdown links when available.
4. If there are multiple plausible matches, ask the user which one they mean before taking any action.

$ARGUMENTS
