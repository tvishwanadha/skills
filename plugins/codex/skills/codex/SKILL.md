---
name: codex
description: >-
  Codex MCP tool usage, thread configuration, and thread lifecycle. Consult when
  using Codex for code review, plan review, or completion verification.
user-invocable: false

---

# Codex MCP Guide

Reference for using the Codex MCP tools (`codex` and `codex-reply`).

## Constraints

- **Do not set the model parameter** - the user has already configured their preferred default
- **Project-only file access** - can only read files in the current project

If content is outside the project, inline it in your prompt.

## Thread Configuration

When starting a thread with the `codex` tool, configure these parameters:

**approval-policy** - Controls shell command approval:
- `never` - **Always use this.** Claude and Codex don't support interactive approval
  flows properly - other options will cause the thread to hang.

**sandbox** - Controls filesystem and network access:
- `read-only` - Read files only, no writes/commands/network (safest for pure review)
- `workspace-write` - Read/write in workspace, run commands, optionally enable network
  (use if Codex needs to run tests or fetch dependencies)
- `danger-full-access` - No sandbox, no approvals (not recommended)

**Important:** These cannot be changed after starting a thread.

**Recommended defaults:**
- `approval-policy: never` (required - other values break)
- `sandbox: read-only` for pure review tasks
- `sandbox: workspace-write` if Codex needs to run tests or access network

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
