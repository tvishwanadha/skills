---
name: review-codex
description: >-
  This skill should be used when the user asks to "review with codex",
  "codex review", or "deep code review".
  Review code for correctness and issues using Codex MCP tools.
allowed-tools: Read, Glob, Grep, Skill, mcp__plugin_codex_codex__codex, mcp__plugin_codex_codex__codex-reply
argument-hint: "[file or directory]"
---

# Review: codex

Review code for correctness and issues using deep analysis powered by Codex MCP tools.

**Input**: `$ARGUMENTS` - file paths or directory to scope the review. If no argument, review the current working directory.

## Dependencies

This skill requires two other plugins to be installed and enabled:

| Plugin | Required for |
|--------|-------------|
| `reviewer` | `reviewer-framework` skill - output format, severity levels, confidence scoring |
| `codex` | `codex:review` skill and Codex MCP tools (`codex`, `codex-reply`) |

## Examples

- `/reviewer-extras:review-codex src/` - deep review of files in the src directory
- `/reviewer-extras:review-codex path/to/file` - deep review of a specific file

## Review Rules

### Prompt Construction

Embed the loaded reviewer-framework content in the Codex prompt so findings come back already structured. Include these sections from the framework:
- **Output Format** - the exact finding template (`[SEVERITY] Category: ...`)
- **Severity Levels** - the four definitions (critical, high, medium, low)
- **Confidence Scoring** - the 0-100 scale with range meanings

Instruct Codex to group findings by severity and end with a summary line.

### Finding Validation

- Verify Codex findings have valid file:line references (spot-check with Read/Grep)
- Discard findings that reference non-existent files or lines
- Adjust confidence scores if Codex's language suggests uncertainty it didn't self-score

## Review Procedure

1. **Determine scope** from `$ARGUMENTS`
   - File paths: review those files directly
   - Directory: review files in the directory (use Glob to discover)
   - No argument: review the current working directory

2. **Read target files** and build a file summary

3. **Load skills** - invoke via the Skill tool:
   - `reviewer:reviewer-framework` for output format, severity definitions, and confidence scoring
   - `codex:review` for the Code Review workflow and Codex thread management

   If neither skill loads, stop and report a single finding:
   ```
   [CRITICAL] Setup: reviewer-extras plugin dependencies are not installed (confidence: 99)
     Details: This skill requires the `reviewer` and `codex` plugins. Install them and restart Claude Code.
   ```

4. **Run Codex review** - follow the Code Review workflow from the loaded `codex:review` skill. Embed the framework's Output Format, Severity Levels, and Confidence Scoring sections in the Codex prompt so findings come back already structured.

5. **Validate findings** - apply the finding validation rules above to verify Codex output

6. **Report findings** using the reviewer-framework output format
