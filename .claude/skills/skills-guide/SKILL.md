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
- **`argument-hint`** - bracket notation (e.g., `[path-to-file]`); use `$ARGUMENTS` in the body to consume the argument
- **`context`** - use `fork` only with actionable task instructions, not guideline-only content

## Content Rules

- Under 500 lines; use supporting files (`references/`, `assets/`) for detailed content
- Step-by-step instructions, not walls of text
- If `argument-hint` is set, define behavior when no argument is provided
- All file references must use relative paths and resolve to existing files
- Prefer the `.md` form of any doc URL over its rendered HTML page - the consumer is an agent that fetches it

## Agent-Facing Content

SKILL.md is read by the agent executing the skill, not by a human browsing docs. Cut on sight:

- Justification/rationale - sentences explaining why a rule exists
- Reassurance - meta-claims about coverage or safety
- Illustrative examples that explain mechanism rather than direct action
- Tool names as guidance - describe the capability and let the model pick the tool (naming tools in `allowed-tools` is fine - that is a permission grant, not guidance)

Write imperatives. If a sentence explains why or that something is true rather than telling the agent what to do, delete it.

When revising a skill, do not patch-fix - bolting a clause onto the skill per reviewer finding turns it to sediment. Rewrite the section from its goal and audience in one pass; test every line by "does an agent need this to act, and can it not derive it?".

Before splitting a skill into sub-sections or separate skills, check that the parts have divergent mechanics. Identical mechanism with varying input, stance, or trigger is one capability - keep it as one skill.

## Key Anti-patterns

- Vague descriptions ("Helps with X") - poor auto-invocation matching
- Auto-invocation on side-effect workflows - risk of unintended actions
- Over-permissive `allowed-tools` - unnecessary security surface
- `context: fork` on guideline-only content - wastes a fork on passive reference

## Local Skill Conventions

- Do not add `disable-model-invocation` to orchestration or review skills (e.g., `self-review`) - auto-invocation is intentional even if resource-heavy

## Full Reference

- Agent Skills open standard (the portable `SKILL.md` spec): https://agentskills.io/specification.md - some frontmatter fields above are harness-specific extensions on top of this core
- Claude Code's field spec, covering those extensions: https://code.claude.com/docs/en/skills.md
- For the review checklist and output format, consult the `plugin-dev:skill-development` skill if available
