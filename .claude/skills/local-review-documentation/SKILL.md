---
name: local-review-documentation
description: Project-specific documentation review rules.
user-invocable: false
---

# Local Review: documentation

These rules extend the default documentation review rules.

## Rules

- Consult the `plugins-guide` skill via the Skill tool for marketplace registration conventions
- Verify cross-reference accuracy (use Glob and Read to confirm):
  - `AGENTS.md` directory layout matches actual repo structure
  - `AGENTS.md` guide skills table lists all guide skills in `.claude/skills/` (Glob for `*-guide` directories)
  - Root `README.md` plugin table matches `.claude-plugin/marketplace.json` entries
  - `CLAUDE.md` references `AGENTS.md`
