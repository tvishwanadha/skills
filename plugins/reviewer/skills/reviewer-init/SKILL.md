---
name: reviewer-init
description: >-
  This skill should be used when the user asks to "set up reviewer", "initialize
  reviews", "configure the reviewer plugin", or "set up code review". Interactive
  setup wizard for the reviewer plugin.
allowed-tools: Read, Glob, Grep, Skill
---

# Reviewer Init

Run an interactive setup wizard for the reviewer plugin. Detect existing configuration, guide through setup choices, and compose the individual creation skills to build the project's review configuration.

## Examples

- `/reviewer:reviewer-init` - run the interactive setup wizard

## Procedure

### 1. Detect existing setup

Scan for existing reviewer configuration:
- Glob for `local-review-*` directories in `.claude/skills/`
- Check for `.claude/skills/self-review-extension/SKILL.md`
- Glob for custom `review-*` directories in `.claude/skills/` (excluding the 4 built-in types)
- Glob for custom agent files in `.claude/agents/`

Present a summary of what already exists.

### 2. Present menu

Show available setup options (multi-select). Mark already-configured items:

1. **Self-review extension** - customize which review types run, agents, threshold
2. **Local review overrides** - add project-specific rules for built-in types (logic, patterns, documentation, skill)
3. **Custom review type** - create a new review category (e.g., security, performance)
4. **Custom reviewer agent** - create a specialized agent with a specific model

### 3. Execute selected options

For each selected option, invoke the corresponding creation skill via the Skill tool:

| Selection | Skill to invoke | Arguments |
|-----------|----------------|-----------|
| Self-review extension | `reviewer:extend-self-review` | (none) |
| Local review overrides | `reviewer:customize-core-review` | `<type>` for each selected type |
| Custom review type | `reviewer:add-core-review` | `<name>` |
| Custom reviewer agent | `reviewer:create-reviewer-agent` | `<name>` |

Invoke skills sequentially - each one is interactive and needs user input.

### 4. Print summary

After all selected options are complete, print a summary of what was created:
- Files created or modified
- How the configuration affects the review workflow
- Next steps (e.g., "Run `/reviewer:self-review` to test your configuration")
