---
name: review-skill
description: >-
  This skill should be used when the user asks to "review a skill", "audit skill
  quality", "check a SKILL.md", or "validate skill conventions". Review SKILL.md
  files for quality, completeness, and best practices.
allowed-tools: Read, Glob, Grep, Skill, WebFetch
argument-hint: "[path-to-skill]"
---

# Review: Skill Quality

Review SKILL.md files against the skill authoring checklist and conventions.

**Input**: `$ARGUMENTS` - path to a skill directory or SKILL.md file. If a directory is given, look for `SKILL.md` inside it. If no argument, ask the user which skill to review.

## Examples

- `/reviewer:review-skill plugins/reviewer/skills/review-logic/` - review a skill directory
- `/reviewer:review-skill path/to/SKILL.md` - review a specific SKILL.md file

## Loading Strategy

1. Try to invoke the skill `local-review-skill` using the Skill tool.
   - If it loads and its instructions say to NOT use the defaults, use only the local skill's guidance. Skip step 2.
   - If it loads and does NOT prohibit defaults, continue to step 2, combining the local guidance with the defaults.
   - If it does not load (skill not found), continue to step 2.

2. Read the default rules from [references/default-skill.md](references/default-skill.md).

## Review Procedure

1. **Locate the SKILL.md** from `$ARGUMENTS`
   - Directory path: read `<path>/SKILL.md`
   - File path: read directly
   - No argument: ask the user

2. **Read the file** and parse frontmatter (YAML between `---` fences) and body content

3. **Apply loaded review rules** - run through every checklist item from the loaded guidance (defaults, local, or combined)

4. **Verify file integrity** - use Glob and Read to check that all referenced files exist; use Grep to verify `$ARGUMENTS` usage and file path references

5. **Report findings** using the reviewer-framework output format (severity, confidence, category, file:line, description, suggestion)

## Upstream References

For edge cases not covered by the checklist, fetch these upstream sources using WebFetch:

- **Primary** (wins on conflicts): `https://code.claude.com/docs/en/skills.md`
- **Secondary**: `https://agentskills.io/specification.md`

Only fetch when a field's constraints are unclear, you encounter an uncovered pattern, or the skill uses advanced features worth double-checking.
