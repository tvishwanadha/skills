---
name: extend-self-review
description: >-
  This skill should be used when the user asks to "customize self-review",
  "change review types", "adjust confidence threshold", "add review types to
  self-review", or "configure the review orchestrator". Create or modify a
  self-review-extension skill.
allowed-tools: Read, Write, Edit, Glob, Grep
---

# Extend Self-Review

Create or modify a `self-review-extension` skill to customize which review types run, agent assignments, confidence threshold, and pre/post-review steps.

## Examples

- `/reviewer:extend-self-review` - create or modify the self-review extension

## Procedure

### 1. Check for existing extension

Use Glob to check if `.claude/skills/self-review-extension/SKILL.md` exists.

If it exists, read and present its current content.

### 2. Show built-in defaults

Present the self-review defaults that the extension can modify:

- **Review types**: `review-logic`, `review-patterns`, `review-documentation` (+ `review-skill` when SKILL.md files are in scope)
- **Agent assignments**:
  - `reviewer:reviewer` (opus) for `review-logic` and `review-skill`
  - `reviewer:simple-reviewer` (sonnet) for `review-patterns` and `review-documentation`
- **Confidence threshold**: >= 80

### 3. Discover custom review types

Use Glob to find `review-*` directories in `.claude/skills/` that aren't the 4 built-in types. Present any discovered custom types as candidates to add.

### 4. Guide through modifications

Ask the user what to change (multi-select):

- **Add review types** - include custom types discovered above (ask which agent for each)
- **Remove review types** - exclude specific defaults
- **Change agent assignments** - switch between `reviewer:reviewer`, `reviewer:simple-reviewer`, or a custom agent
- **Adjust confidence threshold** - raise or lower from the default 80
- **Add pre-review steps** - actions to run before launching reviews (e.g., build, lint)
- **Add post-review steps** - actions to run after coalescing findings

### 5. Generate and write

Read the template from [assets/self-review-extension.md](assets/self-review-extension.md). Replace placeholders:

| Placeholder | Value |
|-------------|-------|
| `{PROJECT}` | Project name (infer from directory or ask) |
| `{MODIFICATIONS}` | Bullet list of all selected modifications |

Write the result to `.claude/skills/self-review-extension/SKILL.md`.

### 6. Confirm

Print what was created and how it affects the self-review workflow.
