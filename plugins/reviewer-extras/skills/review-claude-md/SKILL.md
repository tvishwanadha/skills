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

Review project context files (CLAUDE.md, AGENTS.md, and variants) for quality, currency, and inter-file relationships.

**Input**: `$ARGUMENTS` - file paths or a directory to scope the review. If no argument, review the current working directory. When `reviewer:self-review` invokes this on a diff, the changed files are the trigger, not the target: review the context files that govern or reference them, even though those files are outside the diff.

## Dependencies

| Plugin | Required for |
|--------|-------------|
| `reviewer` | `reviewer-framework` - output format, severity, confidence |
| `claude-md-management` | `claude-md-improver` quality rubric (`references/quality-criteria.md`) |

## Examples

- `reviewer-extras:review-claude-md src/` - review context files governing src/
- `reviewer-extras:review-claude-md path/to/file` - review a specific context file

## Review Rules

### Discovery
- Context files: `CLAUDE.md`, `.claude.md`, `.claude.local.md`, `AGENTS.md`, and `~/.claude/CLAUDE.md` global defaults.
- **Relevant, not just in-scope.** For a diff scope, walk each changed path's directory ancestry to the repo root and collect the context files that govern it, plus any context file that references the changed paths, commands, or symbols (Grep for them). For a directory or file scope, collect the context files within it.
- Deduplicate identical content (compare same-named files across directories); score each unique file once.

### Classification
- Group by parent directory. A file is secondary only if it has a sibling context file in the same directory; otherwise it is primary.
- For a sibling pair, the file whose main purpose is referencing the other (Grep for `CLAUDE.md` / `AGENTS.md` mentions) with minimal own content is secondary; the one holding the bulk is primary. When unsure, treat as primary.

### Primary file assessment
- Score against the rubric in `references/quality-criteria.md`: Commands/Workflows (20), Architecture (20), Non-obvious Patterns (15), Conciseness (15), Currency (15), Actionability (15). Report the score; flag criteria below threshold.
- In a diff scope, weigh Currency and Actionability against the change: does it break a documented command, a file or path reference, or an architecture description? Frame each such finding as the change staling the context file (e.g. "renaming `x.ts` leaves `AGENTS.md:42` pointing at a missing path"), so it reads as the diff breaking content elsewhere.

### Secondary file assessment
- References the primary appropriately, does not duplicate it, carries only target-specific additive guidance, stays concise.

### Relationship health
- No cyclic references between siblings; clear separation of concerns; no contradictory instructions between primaries.

## Review Procedure

1. **Determine scope** from `$ARGUMENTS` - changed files (a diff, from `self-review`), explicit paths, a directory, or the current working directory.

2. **Load the rubric (read-only).** Load `reviewer:reviewer-framework` for the output format, and `claude-md-management:claude-md-improver`; read its `references/quality-criteria.md`. Use only its assessment criteria - do not run the improver's discovery, report, or update phases. This review writes nothing.

   If either dependency fails to load, stop and report a single finding:
   ```
   [CRITICAL] Setup: reviewer-extras dependencies are not installed (confidence: 99)
     Details: review-claude-md needs both `reviewer@teja-skills` and `claude-md-management@claude-plugins-official` enabled in settings.json.
   ```

3. **Discover context files** relevant to the scope per the Discovery rules (ancestry + references for a diff; within-path otherwise). Deduplicate and classify.

4. **Assess** each file - score primaries against the rubric, check secondaries and relationship health. In a diff scope, prioritize currency against the change.

5. **Verify findings** - confirm each referenced file and line exists (Grep/Read) rather than guessing.

6. **Report** findings using the reviewer-framework output format.
