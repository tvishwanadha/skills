---
name: review-documentation
description: >-
  This skill should be used when the user asks to "review docs", "check README
  accuracy", "audit documentation", or "review API docs". Review documentation
  for accuracy, completeness, and quality.
allowed-tools: Read, Glob, Grep, Skill
argument-hint: "[file or directory]"
---

# Review: Documentation

Review documentation for accuracy, completeness, structure, and quality.

**Input**: `$ARGUMENTS` - file paths or directory to scope the review. If no argument, review documentation files in the current working directory. Diff-scoping is handled by the orchestrator (`self-review`), which resolves diffs to file lists before invoking this skill.

## Examples

- `/reviewer:review-documentation docs/` - review docs in a directory
- `/reviewer:review-documentation README.md CHANGELOG.md` - review specific doc files

## Loading Strategy

1. Try to invoke the skill `local-review-documentation` using the Skill tool.
   - If it loads and its instructions say to NOT use the defaults, use only the local skill's guidance. Skip step 2.
   - If it loads and does NOT prohibit defaults, continue to step 2, combining the local guidance with the defaults.
   - If it does not load (skill not found), continue to step 2.

2. Read the default rules from [references/default-documentation.md](references/default-documentation.md).

## Review Procedure

1. **Determine scope** from `$ARGUMENTS`
   - File paths: review those files directly
   - Directory: find documentation files (README.md, CHANGELOG.md, docs/, *.md) in the directory
   - No argument: review documentation files in the current working directory

2. **Read target documentation** and understand what it describes

3. **Load skills** - invoke `reviewer:reviewer-framework` via the Skill tool for output format, severity definitions, and confidence scoring

4. **Cross-reference with code** - use Glob/Grep/Read to verify that documented features, APIs, file paths, and examples match the actual codebase

5. **Apply loaded review rules** - check each rule from the loaded guidance (defaults, local, or combined)

6. **Report findings** using the reviewer-framework output format
