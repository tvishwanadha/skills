---
name: codex
description: >-
  Codex MCP tool usage, thread configuration, and thread lifecycle. Consult this
  skill when an agent or skill needs Codex MCP tool reference for code review,
  plan review, or completion verification.
user-invocable: false
---

# Codex MCP Guide

Reference for using the Codex MCP tools (`codex` and `codex-reply`).

## Constraints

- **Do not set the model parameter** - the user has already configured their preferred default
- **Project-only file access** - can only read files in the current project

If content is outside the project, inline it in your prompt.

## Thread Configuration

Set at thread start; immutable afterward.

- **approval-policy** - Keep the default; approvals surface inline via MCP elicitation.
- **sandbox** - `read-only` for pure review; `workspace-write` when Codex should run tests or edit files.

## Thread Management

**Start thread**: Call the Codex MCP server's `codex` tool with your prompt and configuration.
The response includes:
- `content`: Codex's response
- `threadId`: Save for follow-ups

**Continue thread**: Use the `codex-reply` tool with:
- `threadId`: The thread ID from the previous response
- `prompt`: Your follow-up message

**Surviving context compaction**: Store thread info in your plan file:
```
## Active Codex Thread
- threadId: <id>
- sandbox: read-only
- purpose: plan review / code review / completion verification
```
This ensures you can resume the thread after compaction.

**Lost thread ID**: If not stored in plan file, start a fresh thread with context recovery info.
