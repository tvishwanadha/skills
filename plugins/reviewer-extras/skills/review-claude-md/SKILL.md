---
name: review-claude-md
description: >-
  This skill should be used when the user asks to "review CLAUDE.md", "check CLAUDE.md quality",
  "audit project context files", "review AGENTS.md".
  Review CLAUDE.md and project context files for quality, structure, and inter-file relationships.
allowed-tools: Read, Glob, Grep, Skill
argument-hint: "[file or directory]"
---

# Review: CLAUDE.md

Review CLAUDE.md and project context files for quality, structure, and inter-file relationships.

**Input**: `$ARGUMENTS` - file paths or directory to scope the review. If no argument, review the current working directory.

## Dependencies

This skill requires two other plugins to be installed and enabled:

| Plugin | Required for |
|--------|-------------|
| `reviewer` | `reviewer-framework` skill - output format, severity levels, confidence scoring |
| `claude-md-management` | `claude-md-improver` skill - CLAUDE.md quality rubric |

## Examples

- `/reviewer-extras:review-claude-md src/` - review context files in the src directory
- `/reviewer-extras:review-claude-md path/to/file` - review a specific context file

## Review Rules

### Discovery and Deduplication
- Discover all CLAUDE.md, .claude.md, .claude.local.md, and AGENTS.md files within scope. All of these are project context files loaded by Claude Code and scored with the same rubric
- Detect identical content (read and compare files with the same name in different directories)
- Group duplicates; score each unique file only once

### Classification
- Group discovered files by parent directory
- A file can only be secondary if it has a sibling context file in the same directory
- Files without siblings in the same directory are always primary
- For sibling pairs: check if one file's main purpose is referencing the other (Grep for "AGENTS.md", "CLAUDE.md" mentions). The referencing file with minimal own content is secondary; the referenced file with the bulk of the content is primary
- When unsure, treat as primary

### Primary File Assessment
- Score primary files using the claude-md-improver quality rubric (6 criteria, weighted to 100)
- Report the score and flag criteria below thresholds

### Secondary File Assessment
- References the primary file appropriately
- Does not duplicate content from primary
- Contains only target-specific additive guidance
- Is concise

### Relationship Health
- No cyclic references between sibling files (A references B and B references A)
- Clear separation of concerns between sibling files
- No contradictory instructions between primary files

## Review Procedure

1. **Determine scope** from `$ARGUMENTS`
   - File paths: review those files directly
   - Directory: review files in the directory (use Glob to discover)
   - No argument: review the current working directory

2. **Discover context files** - Glob for CLAUDE.md, .claude.md, .claude.local.md, and AGENTS.md within scope

3. **Load skills** - invoke via the Skill tool:
   - `reviewer:reviewer-framework` for output format, severity definitions, and confidence scoring
   - `claude-md-management:claude-md-improver` for the CLAUDE.md quality rubric

   If either skill fails to load, stop and report a single finding:
   ```
   [CRITICAL] Setup: reviewer-extras plugin dependencies are not installed (confidence: 99)
     Details: At least one required dependency is not installed. Ensure both `reviewer@teja-skills` and `claude-md-management@claude-plugins-official` are enabled in settings.json.
   ```

4. **Apply review rules** - dedupe, classify, score primaries, assess secondaries, and check relationship health per the rules above

5. **Verify findings** - use Grep/Read to confirm issues rather than guessing

6. **Report findings** using the reviewer-framework output format
