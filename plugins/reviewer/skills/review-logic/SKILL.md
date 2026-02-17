---
name: review-logic
description: >-
  Review code for logic errors, control flow issues, edge cases, error handling,
  and state management. Use when asked to review logic, check for bugs, or audit
  error handling.
allowed-tools: Read, Glob, Grep, Skill
argument-hint: "[file or directory]"
---

# Review: Logic

Review code for correctness - control flow, edge cases, error handling, state management, and logical consistency.

**Input**: `$ARGUMENTS` - file paths or directory to scope the review. If no argument, review the current working directory. Diff-scoping is handled by the orchestrator (`self-review`), which resolves diffs to file lists before invoking this skill.

## Examples

- `/reviewer:review-logic src/` - review logic in the src directory
- `/reviewer:review-logic src/handler.ts src/utils.ts` - review specific files

## Loading Strategy

1. Try to invoke the skill `local-review-logic` using the Skill tool.
   - If it loads and its instructions say to NOT use the defaults, use only the local skill's guidance. Skip step 2.
   - If it loads and does NOT prohibit defaults, continue to step 2, combining the local guidance with the defaults.
   - If it does not load (skill not found), continue to step 2.

2. Read the default rules from [references/default-logic.md](references/default-logic.md).

## Review Procedure

1. **Determine scope** from `$ARGUMENTS`
   - File paths: review those files directly
   - Directory: review files in the directory (use Glob to discover)
   - No argument: review the current working directory

2. **Read target files** and understand the code's purpose and flow

3. **Apply loaded review rules** - check each rule from the loaded guidance (defaults, local, or combined) against the code

4. **Verify findings** - use Grep/Read to confirm issues rather than guessing; trace control flow paths to validate edge case concerns

5. **Report findings** using the reviewer-framework output format (severity, confidence, category, file:line, description, suggestion)
