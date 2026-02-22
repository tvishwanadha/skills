---
name: skills-guide
description: >-
  This skill should be loaded when creating a skill, modifying a SKILL.md,
  writing skill frontmatter, reviewing skill quality, or authoring skill
  descriptions. Defines conventions for SKILL.md files - frontmatter, content
  structure, and review criteria.
user-invocable: false

---

# Skill Conventions - Quick Reference

## Frontmatter Essentials

- **`name`** - lowercase with hyphens, must match containing directory name
- **`description`** - include keywords for auto-invocation; describe **what** and **when**; see `plugin-dev:skill-development` for description format conventions
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

For the complete review checklist, output format, and upstream references, consult the `plugin-dev:skill-development` skill.
