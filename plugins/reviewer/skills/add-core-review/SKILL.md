---
name: add-core-review
description: >-
  This skill should be used when the user asks to "add a review type", "create
  a custom review", "add security review", "add performance review", or "create
  a new review category". Create a new custom review type with project-specific
  rules.
allowed-tools: Read, Write, Edit, Glob, Grep
argument-hint: "[type-name (e.g., security, plugin, codex)]"
---

# Add Core Review

Create a new custom review type. Add project-specific review categories like security, performance, accessibility, or domain-specific checks.

**Input**: `$ARGUMENTS` - name for the new review type (e.g., `security`, `plugin`, `codex`).

## Examples

- `/reviewer:add-core-review security` - create a security review type
- `/reviewer:add-core-review plugin` - create a plugin-specific review type

## Procedure

### 1. Parse and validate name

Extract the type name from `$ARGUMENTS`. Validate:
- Not empty (ask if missing)
- If name starts with `review-`, strip the prefix (e.g., `review-security` becomes `security`)
- Not a built-in type name (`logic`, `patterns`, `documentation`, `skill`)
- Lowercase, hyphens allowed, no spaces

### 2. Check for existing skill

Use Glob to check if `.claude/skills/review-<name>/SKILL.md` already exists.

If it exists:
1. Read the file and present its current content
2. Ask: **replace** (overwrite), **update** (modify rules), or **cancel**
3. If cancel, stop here

### 3. Gather review definition

Ask the user for:
- **Focus area** - what this review checks for (1-2 sentences)
- **Auto-invocation keywords** - verb phrases that should trigger this review (e.g., "check for security issues", not just "security")
- **Review rules** - categorized bullet list of specific checks
- **Additional tools** - any tools beyond Read, Glob, Grep, Skill (e.g., Bash for running linters)

### 4. Generate and write

Read the template from [assets/review-type.md](assets/review-type.md). Replace placeholders:

| Placeholder | Value |
|-------------|-------|
| `{NAME}` | The review type name |
| `{FOCUS}` | What this review checks for |
| `{KEYWORDS}` | Auto-invocation trigger phrases |
| `{RULES}` | Categorized bullet list of review rules |

If additional tools were specified, add them to the `allowed-tools` frontmatter field.

Write the result to `.claude/skills/review-<name>/SKILL.md`.

### 5. Auto-wire into self-review extension

Check if `.claude/skills/self-review-extension/SKILL.md` exists:

- **If yes**: read it, show current content, and offer to add the new review type. Ask which agent to use (`reviewer:reviewer` or `reviewer:simple-reviewer`). Note: if the review type requires tools beyond the standard reviewer agents (e.g., Bash, MCP tools), recommend creating a custom agent via `/reviewer:create-reviewer-agent` instead. If accepted, edit the extension to include the new type.
- **If no**: print instructions suggesting `/reviewer:extend-self-review` to configure the orchestrator to include this new type.

### 6. Confirm

Print what was created and how to invoke it (e.g., `/review-<name> src/`).
