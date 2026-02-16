---
name: review
description: >-
  Use Codex MCP for plan review, completion verification, and code review on
  multi-file changes or after context compactions.

---

# Codex Review

Consult the `codex` skill for MCP tool usage, thread configuration, and thread lifecycle.

- **Read-only reviewer** - provides feedback, you make the changes

## When to Use

**Use Codex for:**
- Multi-file implementation plans
- Architectural decisions or complex refactors
- Completion verification after context compactions

**Skip for:** simple docs, single-file changes, trivial fixes.

## Process

All workflows follow this loop:

1. **Send request** using the Codex MCP server's `codex` tool (first call) or `codex-reply` tool (follow-ups)
2. **Receive feedback** from Codex
3. **Address feedback** - make changes or push back with reasoning
4. **Re-request review** until approved

Expect multi-minute responses; don't cancel early. Not all feedback must be incorporated - push back on overly pedantic points with reasoning.

## Workflows

### Plan Review

Use before exiting plan mode.

```
Context: [user's request and goals]
Scope: [in/out of scope]
Constraints: [requirements]
Plan: [path or inline content]

Review for completeness and correctness. Respond APPROVED if solid.
```

### Completion Verification

Use before telling the user work is complete, especially after:
- Context compaction during implementation
- Multi-file or multi-step tasks
- Long conversations

```
Implementation complete. Examine files on disk and verify:
- All planned changes were made
- No partial implementations remain
- No steps discussed but not executed

List anything incomplete.
```

If incomplete, fix and re-verify.

### Code Review

Use after writing significant code.

```
Purpose: [what the code should do]
Files: [paths or inline content]
Concerns: [specific areas to check]
Test results: [if available]

Review for correctness and issues.
```
