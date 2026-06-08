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

- `reviewer:extend-self-review` - create or modify the self-review extension

## Procedure

> `<local-skills>/` below is the project's local skills directory (e.g. `.claude/skills/`); prefer one that already exists.

### 1. Check for existing extension

Check if `<local-skills>/self-review-extension/SKILL.md` exists on disk.

If it exists, read and present its current content.

### 2. Show built-in defaults

Read the "Built-in Defaults" section of `reviewer:self-review` and present it to the user - review types, agent assignments, and confidence threshold.

### 3. Discover custom review types

Search on disk for `review-*` directories in `<local-skills>/` that aren't the 4 built-in types. Present any discovered custom types as candidates to add.

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

Write the result to `<local-skills>/self-review-extension/SKILL.md`.

### 6. Confirm

Print what was created and how it affects the self-review workflow.
