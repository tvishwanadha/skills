---
name: review-{NAME}
description: >-
  This skill should be invoked by a review orchestrator (e.g.
  `reviewer:self-review`) to review code for {FOCUS}.
allowed-tools: Read, Glob, Grep, Skill
argument-hint: "[file or directory]"
---

# Review: {NAME}

Review code for {FOCUS}.

**Input**: `$ARGUMENTS` - file paths or directory to scope the review. If no argument, review the current working directory. Treat `$ARGUMENTS` as file paths; do not parse diff refs.

## Examples

- `review-{NAME} src/` - review files in the src directory
- `review-{NAME} path/to/file` - review a specific file

## Review Rules

{RULES}

## Review Procedure

1. **Determine scope** from `$ARGUMENTS`
   - File paths: review those files directly
   - Directory: review files in the directory (use Glob to discover)
   - No argument: review the current working directory

2. **Load rules and framework** - read target files to understand context, and load `reviewer:reviewer-framework` for output format, severity definitions, and confidence scoring

3. **Apply review rules** - check each rule above against the code

4. **Verify findings** - search the codebase to confirm issues rather than guessing

5. **Report findings** using the reviewer-framework output format
