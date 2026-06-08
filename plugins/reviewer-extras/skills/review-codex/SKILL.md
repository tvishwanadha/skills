---
name: review-codex
description: >-
  This skill should be used when the user asks to "review with codex",
  "codex review", or "deep code review".
  Run a parallel self-review inside a Codex thread for an independent second opinion.
allowed-tools: Read, Glob, Grep, Skill, mcp__plugin_codex_codex__codex, mcp__plugin_codex_codex__codex-reply
argument-hint: "[file, directory, or --diff <ref>]"
---

# Review: Codex

Dispatch `reviewer:self-review` into a Codex thread so Codex runs its own parallel reviewers - the same review on a second harness, for an independent second opinion. The dispatch appends `--no-fix`, so Codex returns a report only; fixing stays on the host. If `reviewer` is not available inside Codex, fall back to a single-pass review via `codex:review` and convert its findings into the reviewer framework.

**Input**: `$ARGUMENTS` - file paths, directory, or `--diff <ref>` (same syntax as `reviewer:self-review`). If no argument, review the current diff.

## Dependencies

| Plugin | Required for | Side |
|--------|-------------|------|
| `codex` | `codex:review` skill and Codex MCP tools (`codex`, `codex-reply`) | Claude (host) |
| `reviewer` | `reviewer-framework` output format (both paths) | Claude (host) |
| `reviewer` | `$reviewer:self-review` orchestrator (preferred path; its absence triggers the fallback, not a failure) | Codex (target) |

## Examples

- `reviewer-extras:review-codex src/` - dispatch a self-review of src/ to Codex
- `reviewer-extras:review-codex --diff main` - dispatch a diff-scoped self-review to Codex
- `reviewer-extras:review-codex` - dispatch a self-review of the current diff to Codex

## Procedure

1. **Determine scope** from `$ARGUMENTS` - pass file paths, directories, `--diff <ref>`, or empty straight through; both paths hand the scope to the Codex side to resolve.

2. **Preflight host dependencies** - load `codex:review` and `reviewer:reviewer-framework`. If either fails to load, stop and report a single finding:
   ```
   [CRITICAL] Setup: reviewer-extras dependencies are not installed (confidence: 99)
     Details: review-codex needs both `codex@teja-skills` and `reviewer@teja-skills` enabled in settings.json.
   ```

3. **Preferred path - dispatch the bare command.** Send this as the Codex prompt with no surrounding text, on a read-only thread per the `codex` skill:
   ```
   $reviewer:self-review <scope> --no-fix
   ```
   Expect multi-minute responses; do not cancel early.

4. **Inspect the response.**
   - Begins with `# Self-Review Report` → self-review ran inside Codex; its findings are already in reviewer-framework format. Go to step 6.
   - Anything else (an error, an apology, a refusal, or a description of the slash command rather than its output) → `reviewer` is not available in Codex, or self-review failed there. Go to step 5.

5. **Fallback - single-pass via `codex:review`.** Invoke `codex:review` over the same scope, then convert each finding it returns into reviewer-framework format: its location → `file:line`, its description and trigger conditions → Details, its fix → Suggestion, with a severity and confidence (treat findings Codex labels `(inference)` as lower confidence).

6. **Validate findings.** Confirm each finding's `file:line` exists (spot-check by reading the file); discard findings that reference non-existent files or lines; lower confidence if the wording signals uncertainty it did not self-score.

7. **Report** the validated findings, prefixed with a one-line note of which path ran (preferred self-review vs. fallback single-pass via `codex:review`).
