---
name: review-patterns
description: >-
  Review code for naming conventions, structural patterns, style consistency, and
  test quality. Use when asked to review patterns, check conventions, or audit
  code style.
allowed-tools: Read, Glob, Grep, Skill
argument-hint: "[file or directory]"
---

# Review: Patterns

Review code for adherence to naming conventions, structural patterns, style consistency, and test quality.

**Input**: `$ARGUMENTS` - file paths or directory to scope the review. If no argument, review the current working directory. Diff-scoping is handled by the orchestrator (`self-review`), which resolves diffs to file lists before invoking this skill.

## Examples

- `/reviewer:review-patterns src/` - review patterns in the src directory
- `/reviewer:review-patterns src/handler.ts src/utils.ts` - review specific files

## Loading Strategy

1. Try to invoke the skill `local-review-patterns` using the Skill tool.
   - If it loads and its instructions say to NOT use the defaults, use only the local skill's guidance. Skip step 2.
   - If it loads and does NOT prohibit defaults, continue to step 2, combining the local guidance with the defaults.
   - If it does not load (skill not found), continue to step 2.

2. Read the default rules from [references/default-patterns.md](references/default-patterns.md).

## Review Procedure

1. **Determine scope** from `$ARGUMENTS`
   - File paths: review those files directly
   - Directory: review files in the directory (use Glob to discover)
   - No argument: review the current working directory

2. **Read target files** and understand the project's existing conventions by sampling nearby code

3. **Apply loaded review rules** - check each rule from the loaded guidance (defaults, local, or combined) against the code

4. **Compare against project norms** - use Grep to check if flagged patterns are consistent or inconsistent with the rest of the codebase

5. **Report findings** using the reviewer-framework output format (severity, confidence, category, file:line, description, suggestion)
