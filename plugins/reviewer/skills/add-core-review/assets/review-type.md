---
name: review-{NAME}
description: >-
  This skill should be used when the user asks to review {KEYWORDS}.
  Review code for {FOCUS}.
allowed-tools: Read, Glob, Grep, Skill
argument-hint: "[file or directory]"
---

# Review: {NAME}

Review code for {FOCUS}.

**Input**: `$ARGUMENTS` - file paths or directory to scope the review. If no argument, review the current working directory.

## Examples

- `/review-{NAME} src/` - review files in the src directory
- `/review-{NAME} path/to/file` - review a specific file

## Review Rules

{RULES}

## Review Procedure

1. **Determine scope** from `$ARGUMENTS`
   - File paths: review those files directly
   - Directory: review files in the directory (use Glob to discover)
   - No argument: review the current working directory

2. **Read target files** and understand context

3. **Load skills** - invoke `reviewer:reviewer-framework` via the Skill tool for output format, severity definitions, and confidence scoring

4. **Apply review rules** - check each rule above against the code

5. **Verify findings** - use Grep/Read to confirm issues rather than guessing

6. **Report findings** using the reviewer-framework output format (severity, confidence, category, file:line, description, suggestion)
