---
name: review-skill
description: >-
  This skill should be used when the user asks to "review a skill", "audit skill
  quality", "check a SKILL.md", or "validate skill conventions". Review SKILL.md
  files for quality and conventions, and check that a change keeps related
  skills correct.
allowed-tools: Read, Glob, Grep, Skill, WebFetch
argument-hint: "[path-to-skill]"
---

# Review: Skill Quality

Assess how a change impacts skills across the project, and review changed or targeted SKILL.md files against the authoring checklist and conventions.

**Input**: `$ARGUMENTS` - paths. Selects which mode(s) run (step 1):

- A change scope (`self-review` diff or other paths): change-impact mode across the whole project, plus quality mode on any `SKILL.md` files that themselves changed.
- A `SKILL.md` or a directory containing one: quality mode on that skill.
- No argument: quality mode on every skill in the project.

## Examples

- `reviewer:review-skill plugins/reviewer/skills/review-logic/` - review a skill directory
- `reviewer:review-skill path/to/SKILL.md` - review a specific SKILL.md file

## Loading Strategy

1. Try to load the skill `local-review-skill`.
   - If it loads and its instructions say to NOT use the defaults, use only the local skill's guidance. Skip step 2.
   - If it loads and does NOT prohibit defaults, continue to step 2, combining the local guidance with the defaults.
   - If it does not load (skill not found), continue to step 2.

2. Read the default rules from [references/default-skill.md](references/default-skill.md).

## Review Procedure

1. **Resolve scope and mode** from `$ARGUMENTS` per the Input section. Change-impact mode (step 3) runs by default for any change scope; quality mode (step 4) runs on skills that are the explicit target or themselves changed.

2. **Load skills** - load `reviewer:reviewer-framework` for output format, severity definitions, and confidence scoring

3. **Change-impact mode - for any change scope:** read the changed files and flag, across the whole project, only what the change invalidates, in both directions:
   - a change (skill or non-skill) that invalidates a skill's content - a documented claim it makes false, a reference it leaves stale, a renamed or removed thing the skill still describes
   - a skill change that breaks references to it elsewhere - other skills' `[[links]]`, `marketplace.json`, README tables, agent definitions, or orchestration skills that invoke it - via rename, move, removal, or contract change

   Flag only change-driven breakage here; do not run the quality checklist on untouched skills.

4. **Quality mode - for each skill that is the target or itself changed:**
   - **Read the file** and parse frontmatter (YAML between `---` fences) and body content
   - **Apply loaded review rules** - run through every checklist item from the loaded guidance (defaults, local, or combined)
   - **Verify file integrity** - check that all referenced files exist on disk; search file contents to verify `$ARGUMENTS` usage and file path references

5. **Report findings** using the reviewer-framework output format

## Upstream References

For edge cases not covered by the checklist, fetch these upstream sources using WebFetch:

- **Primary** (wins on conflicts): [Claude Code skills spec](https://code.claude.com/docs/en/skills.md)
- **Secondary**: [Agent Skills standard](https://agentskills.io/specification.md)

Only fetch when a field's constraints are unclear, you encounter an uncovered pattern, or the skill uses advanced features worth double-checking.
