---
name: research-agent
description: Stretch goal. When asked to research a topic, use available tools to gather sources, summarize them briefly, and save the result to a notes file. Cite sources and never invent them.
---

# Research Agent (stretch goal)

This is a starter skill for the optional stretch goal. Flesh it out with Claude Code.

## What it should do
- When the user asks about a topic, gather a few sources using whatever tool is available
  (an MCP fetch/search server, or a tool you wrote).
- Summarize each source in one or two sentences.
- Save the summary to `notes.txt`.

## Rules
- Always cite where each fact came from.
- If you can't find real sources, say so. Do not make up sources or facts.
- Keep the saved note under 500 characters per entry.
