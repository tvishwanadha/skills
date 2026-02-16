---
name: skills-guide
description: >-
  Skill authoring conventions for SKILL.md files - frontmatter, content
  structure, and review criteria.
user-invocable: false

---

# Skill Conventions - Quick Reference

## Frontmatter Essentials

- **`name`** - lowercase with hyphens, must match containing directory name
- **`description`** - include keywords for auto-invocation; describe **what** and **when**; keep concise (shared global budget)
- **`allowed-tools`** - comma-space format (`Read, Glob, Grep`); only list tools the skill actually uses; guide skills (`user-invocable: false`) don't need this field
- **`user-invocable`** - set `false` only for background-knowledge/guide skills
- **`disable-model-invocation`** - set `true` for side-effect workflows (deploy, commit, send)
- **`argument-hint`** - bracket notation (e.g., `[path-to-file]`); if present, `$ARGUMENTS` must appear in body
- **`context`** - use `fork` only with actionable task instructions, not guideline-only content

## Content Rules

- Under 500 lines; use supporting files (`reference/`, `assets/`) for detailed content
- Step-by-step instructions, not walls of text
- If `argument-hint` is set, define behavior when no argument is provided
- All file references must use relative paths and resolve to existing files

## Key Anti-patterns

- Vague descriptions ("Helps with X") - poor auto-invocation matching
- Auto-invocation on side-effect workflows - risk of unintended actions
- Over-permissive `allowed-tools` - unnecessary security surface
- `context: fork` on guideline-only content - wastes a fork on passive reference

## Local Skill Conventions

- Do not add `disable-model-invocation` to orchestration or review skills (e.g., `self-review`) - auto-invocation is intentional even if resource-heavy

## Full Reference

The complete review checklist, output format, and upstream references are in the [`skill-reviewer` skill](../../../plugins/skill-reviewer/skills/skill-reviewer/SKILL.md).

## Generic Conventions

For comprehensive Claude Code skill authoring guidance (progressive disclosure, writing style, validation checklist), consult the `plugin-dev:skill-development` skill if plugin-dev is installed.
