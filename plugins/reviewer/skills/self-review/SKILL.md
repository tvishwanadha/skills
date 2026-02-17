---
name: self-review
description: >-
  Orchestrate comprehensive code review by launching reviewer agents in parallel.
  Use when asked to review code, check changes, audit work quality, or run a
  self-review.
allowed-tools: Read, Glob, Grep, Bash, Task, Skill
argument-hint: "[scope: file, directory, or --diff <ref>]"
---

# Self-Review

Orchestrate a comprehensive code review by launching parallel reviewer agents, each running a different review type. Coalesce findings into a unified report.

## Examples

- `/self-review` - review the current working directory
- `/self-review src/` - review files in src/
- `/self-review --diff main` - review changes vs main branch
- `/self-review --diff HEAD~3` - review last 3 commits
- `/self-review path/to/file.ts` - review a specific file

## Built-in Defaults

These defaults apply unless overridden by a `self-review-extension` skill:

- **Review types**: `review-logic`, `review-patterns`, `review-documentation`
  - Additionally include `review-skill` if any SKILL.md files are in scope
- **Agent**: `reviewer:reviewer` (opus) for all review types
- **Confidence threshold**: >= 80

## Procedure

> **All Task calls must be foreground** - never set `run_in_background: true`. Subagents need the Skill tool for conditional loading, which is only available in foreground tasks.

### 1. Parse scope

Determine the review scope from `$ARGUMENTS`:

- **Empty or blank**: review the current working directory
- **Starts with `--diff`**: extract the git ref (token after `--diff`, default `HEAD~1` if missing). Run `git diff --name-only <ref>` via Bash to get changed files.
- **Otherwise**: treat as file path(s) or directory

### 2. Gather target files

- For `--diff` mode: partition the changed file list into **existing files** (still on disk) and **deleted files** (removed in this diff). Existing files are the review scope. Deleted files are passed as context in each Task prompt so reviewers can flag stale references or unintended removals.
- For directory: use Glob to discover files in the directory
- For file paths: use the paths directly

Check if any files in scope are SKILL.md files. In `--diff` mode, check if any changed file paths end with `SKILL.md` (string match on the file list). Otherwise, Glob for `**/SKILL.md` within the target. If SKILL.md files are found, add `review-skill` to the review types list.

### 3. Check for extension

Try to invoke `self-review-extension` via the Skill tool.

If it loads, its instructions take priority over the built-in defaults. The extension can:
- Add or remove review types from the list
- Change agent assignments per review type
- Adjust the confidence threshold
- Add pre/post-review steps

If it does not load (skill not found), use the built-in defaults.

### 4. Launch parallel reviews

For each review type in the final list, launch a Task:

- **`subagent_type`**: the agent name from defaults/extension (default: `"reviewer"` which maps to `reviewer:reviewer`)
- **`prompt`**: instruct the agent to invoke the corresponding review skill (e.g., `reviewer:review-logic`) with the scope as its argument

Launch all Tasks in a single message for parallel execution. Do NOT use `run_in_background: true`.

Example Task prompt for a review type:
```
Invoke the skill `reviewer:review-logic` with argument: src/handler.ts src/utils.ts tests/handler.test.ts

Review scope: files changed vs main branch.

Files to review:
- src/handler.ts
- src/utils.ts
- tests/handler.test.ts

Deleted files (review for stale references, not file contents):
- src/old-handler.ts
```

### 5. Coalesce findings

After all Tasks complete, combine their outputs:

1. Collect all findings from all review types
2. Deduplicate: if multiple reviewers flag the same file:line with the same issue, keep the finding with the highest confidence. Merge context from duplicates.
3. Filter by confidence threshold (>= 80, or as adjusted by extension)
4. Sort by severity (critical > high > medium > low), then by confidence (descending)

### 6. Present report

```
# Self-Review Report

**Scope**: <description of what was reviewed>
**Review types**: <list of review types run>
**Confidence threshold**: >= <threshold>

## Findings

[SEVERITY] Category: Brief description (confidence: N)
  File: <path>:<line>
  Details: What's wrong and why it matters
  Suggestion: Specific fix or improvement
  Found by: <review-type>

...

## Summary

Total: N findings (X critical, Y high, Z medium, W low)
Filtered out: M findings below confidence threshold
```

If no findings meet the threshold, report:

```
# Self-Review Report

**Scope**: <description>
**Review types**: <list>

No issues found above the confidence threshold (>= <threshold>).
<N> findings were filtered out below threshold.
```

### 7. Iterative re-review

If the user fixes issues and asks for re-review, repeat the process. Focus on the previously flagged files unless the user specifies a different scope.
