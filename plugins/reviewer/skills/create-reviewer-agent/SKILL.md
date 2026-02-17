---
name: create-reviewer-agent
description: >-
  This skill should be used when the user asks to "create a reviewer agent",
  "add a custom agent", "create a review agent", or "add a specialized
  reviewer". Create a custom reviewer agent with a specific model and tools.
allowed-tools: Read, Write, Edit, Glob, Grep
argument-hint: "[agent-name]"
---

# Create Reviewer Agent

Create a custom reviewer agent with a specific model, tools, and skills. Add specialized reviewer agents for different review contexts.

**Input**: `$ARGUMENTS` - name for the new agent.

## Examples

- `/reviewer:create-reviewer-agent security-reviewer` - create a security-focused reviewer
- `/reviewer:create-reviewer-agent fast-reviewer` - create a fast haiku-based reviewer

## Procedure

### 1. Parse agent name

Extract the agent name from `$ARGUMENTS`. Validate:
- Not empty (ask if missing)
- Lowercase, hyphens allowed, no spaces or underscores
- 3-50 characters, starts and ends with alphanumeric

### 2. Check for existing agent

Use Glob to check if `.claude/agents/<name>.md` already exists.

If it exists:
1. Read the file and present its current content
2. Ask: **replace** (overwrite), **update** (modify), or **cancel**
3. If cancel, stop here

### 3. Gather agent definition

Ask the user for:
- **Model** - `opus` (deep reasoning), `sonnet` (balanced), or `haiku` (fast)
- **Description** - what this agent specializes in
- **Additional tools** - beyond the default `Read, Glob, Grep, Skill, WebFetch`
- **Additional skills** - beyond `reviewer-framework`
- **Extra instructions** - any specialized behavior to add to the system prompt

### 4. Generate and write

Read the template from [assets/reviewer-agent.md](assets/reviewer-agent.md). Replace placeholders:

| Placeholder | Value |
|-------------|-------|
| `{NAME}` | Agent name |
| `{DESCRIPTION}` | Agent description |
| `{MODEL}` | Selected model |

If additional tools, skills, or instructions were specified, modify the template output accordingly before writing.

Write the result to `.claude/agents/<name>.md`.

### 5. Confirm

Print what was created and how to reference it in self-review extensions or Task calls.
