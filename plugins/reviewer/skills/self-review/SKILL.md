---
name: self-review
description: >-
  This skill should be used when the user asks to "review code", "check changes",
  "audit work quality", "run a self-review", or "review a diff". Orchestrate
  comprehensive code review by launching reviewer agents in parallel.
allowed-tools: Read, Glob, Grep, Bash, Task, Skill
argument-hint: "[scope: file, directory, or --diff <ref>]"
---

# Self-Review

Orchestrate a comprehensive code review by launching parallel reviewer agents, each running a different review type. Coalesce findings into a unified report.

## Examples

- `self-review` - review the current diff (uncommitted changes, or branch vs base if the tree is clean)
- `self-review --diff main` - review changes vs the merge-base with main
- `self-review --diff HEAD~3` - review the last 3 commits
- `self-review src/` - review all files in src/ (whole-file, not diff-scoped)
- `self-review path/to/file.ts` - review a specific file
- `self-review --diff main --no-fix` - review only; skip the fix handoff

## Built-in Defaults

These defaults apply unless overridden by a `self-review-extension` skill:

- **Review types**: `review-logic`, `review-patterns`, `review-documentation`, `review-skill`
- **Agent assignments**:
  - `reviewer:reviewer` (opus) for `review-logic` and `review-skill`
  - `reviewer:simple-reviewer` (sonnet) for `review-patterns` and `review-documentation`
- **Confidence threshold**: >= 80
- **Verification**: always on - every finding that clears the threshold is verified before it appears in the report (see step 6)
- **Fixing**: after the report, hands off to `reviewer:fix-findings` for interactive fixes, unless `--no-fix` is passed or the context is non-interactive (see step 8)

## Procedure

> **Run all reviewers concurrently.** If possible, launch them all in a single message so they run in parallel and their reports return together for coalescing.

### 1. Parse scope

Determine the review scope from `$ARGUMENTS`. The default scope is the diff, not the whole tree - this keeps findings focused on what changed.

First, check for the `--no-fix` flag: if the token `--no-fix` appears anywhere in `$ARGUMENTS`, note it (it suppresses the fix handoff in step 8) and remove it from the string before resolving scope, so it is not mistaken for a file path.

- **Empty or blank**: review the current diff.
  - Run `git diff --name-only HEAD` for tracked changes, and `git ls-files --others --exclude-standard` for untracked (newly created) files. Combine both lists. If non-empty, the scope is the uncommitted changes (staged + unstaged + new files) vs `HEAD`. `git diff HEAD` alone omits untracked files, so the separate `ls-files` step is required.
  - If both are empty (clean tree), fall back to the branch diff vs the default base `<base>` (the default branch - `origin/HEAD` if detectable, otherwise `main`): `b=$(git merge-base <base> HEAD); git diff --name-only "$b"`.
- **Starts with `--diff`**: extract the git ref (token after `--diff`, default `HEAD~1` if missing). Resolve the merge-base, then diff the **working tree** against it, so the scope includes committed branch work plus uncommitted/untracked changes while excluding commits the base has that the branch lacks: `b=$(git merge-base <ref> HEAD); git diff --name-only "$b"`, plus `git ls-files --others --exclude-standard` for untracked files. Do NOT use `git diff <ref>...HEAD` - three-dot compares merge-base to HEAD and silently drops all uncommitted work.
- **Otherwise**: treat as file path(s) or directory. This is whole-file review, not diff-scoped (no hunk focus in step 4).

### 2. Gather target files and hunks

**Diff modes** (the empty default and `--diff <ref>`):

- Partition the changed file list into **existing files** (still on disk) and **deleted files** (removed in this diff). Existing files are the review scope. Deleted files are passed as context in each review prompt so reviewers can flag stale references or unintended removals. If both lists are empty, report "No changes found" (name the ref for `--diff`; for the empty default say "no uncommitted changes and no diff vs `<base>`") and exit without launching review tasks.
- **Capture the hunks.** Run the same `git diff` resolved in step 1, *without* `--name-only` (`git diff HEAD`, or `git diff "$b"` against the merge-base for `--diff` / clean-tree modes), and retain the per-file hunks with their `@@` line markers. Untracked files have no diff entry: treat the whole file as a single added hunk covering all its lines. Keep this in memory - it drives the focus instruction in step 4 and the mechanical filter in step 6.

**Directory**: discover files in the directory. No hunks (whole-file review).

**File paths**: use the paths directly. No hunks (whole-file review).

### 3. Check for extension

Try to load `self-review-extension`.

If it loads, its instructions take priority over the built-in defaults. Extensions are additive - built-in review types are always included unless the extension explicitly removes them. The extension can:
- Add or remove review types from the list
- Change agent assignments per review type
- Adjust the confidence threshold
- Add pre/post-review steps

**Namespace resolution**: built-in types can be referenced with or without the `reviewer:` prefix (e.g., `review-logic` or `reviewer:review-logic`). Custom types from extensions use their skill name directly (e.g., `review-plugin`).

If it does not load (skill not found), use the built-in defaults.

### 4. Launch parallel reviews

For each review type in the final list, spawn a reviewer subagent:

- **Agent**: the reviewer agent from defaults/extension (e.g., `"reviewer"` or `"reviewer:reviewer"` for logic/skill, `"simple-reviewer"` or `"reviewer:simple-reviewer"` for patterns/documentation)
- **Prompt**: instruct the agent to invoke the corresponding review skill (e.g., `review-logic` or `reviewer:review-logic`) with the scope as its argument

**In diff modes**, also include the hunks captured in step 2 plus a focus instruction, so reviewers concentrate on what changed. Reviewers still read whole files for context, but only changed lines should be flagged. In whole-file modes (directory / file paths) there are no hunks - omit the hunks and the focus instruction.

Spawn all reviewers concurrently, not sequentially, under the foreground rule from the callout above.

Example prompt for a reviewer (diff mode):
```
Invoke the skill `reviewer:review-logic` with argument: src/handler.ts src/utils.ts tests/handler.test.ts

Review scope: changes vs the merge-base with main.

Files to review:
- src/handler.ts
- src/utils.ts
- tests/handler.test.ts

Deleted files (review for stale references, not file contents):
- src/old-handler.ts

Focus: review ONLY the changed lines shown in the hunks below. Read the whole
file for context, but do not flag pre-existing code unless this change directly
breaks or worsens it. The only other out-of-hunk findings allowed are about a
deleted file or a reference this change leaves stale.

Changed hunks (per file, with @@ markers):

  src/handler.ts
  @@ -12,7 +12,9 @@ function handle(req) {
     ...
```

### 5. Coalesce findings

After all reviewers return, combine their outputs:

1. Collect all findings from all review types
2. Deduplicate: if multiple reviewers flag the same file:line with the same issue, keep the finding with the highest confidence (tie-break: most specific suggestion). Merge context from duplicates. Accumulate all originating review types in the `Found by` field.
3. Filter by confidence threshold (>= 80, or as adjusted by extension)
4. Sort by severity (critical > high > medium > low), then by confidence (descending)

The result is the set of **survivors** - findings that cleared the threshold. These go to verification.

### 6. Verify findings

Verification always runs on the survivors from step 5. It never adds new findings - it only keeps, drops, or downgrades existing ones.

**Tier 0 - mechanical filter (no agent).** Using the hunks retained in step 2:

- Drop any finding whose `file:line` is not inside a changed hunk, unless the finding names specific unchanged code that this change breaks or worsens (judge by the finding's substance, not whether it is phrased as an argument), or concerns a deleted file / stale reference (deleted-file findings are exempt - they are the reason deleted files are passed as context in step 2).
- Drop findings whose `file:line` does not exist or does not contain what the finding describes (spot-check by reading the file). This does not apply to deleted-file findings.
- Whole-file modes have no hunks: skip the hunk-membership check and apply only the reference-validity check.
- All diff modes diff the working tree (against `HEAD` or the merge-base), so hunk line numbers match what reviewers read on disk; still allow a small line offset before dropping a near-miss.

**Tier 1 - adversarial fanout.** For each finding that survives Tier 0, spawn a `reviewer` (opus) subagent whose job is to *refute*, not re-review. Spawn them concurrently, under the same foreground rule as the callout above. Each prompt contains one finding plus its hunk (in whole-file modes, pass the surrounding file region instead, since there is no hunk) and instructs:

```
Do NOT invoke any review skill. Your job is to refute this finding.
- Findings with a runtime trigger (logic, edge cases, error handling): try to
  construct the input or code path that triggers the bug, or show it cannot
  occur. Return `refuted` if you cannot demonstrate it is real.
- Findings with no executable trigger (documentation, naming, style,
  skill/structure): check whether the claim is factually wrong or absent from
  the code. Return `refuted` only if you can show the claim is false; if you
  can neither confirm nor disprove it, return `uncertain` - never `refuted`
  merely because there is no runtime trigger.
Also judge whether the suggested fix is correct.

Return: verdict (real | refuted | uncertain), adjusted_confidence (0-100),
and a one-line reason.
```

Apply each verdict:

- `refuted`: drop the finding
- `uncertain`: set its confidence to `adjusted_confidence` and re-apply the threshold (drop if it now falls below)
- `real`: keep (use `adjusted_confidence` if higher than the original)

Count every finding dropped at this step - Tier 0 mechanical drops, Tier 1 `refuted` verdicts, and `uncertain` findings that fell below threshold - as a single "dropped at verification" total for step 7.

### 7. Present report

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
Dropped at verification: V findings (mechanical + refuted + re-scored below threshold)
```

If no findings survive (threshold + verification), report:

```
# Self-Review Report

**Scope**: <description>
**Review types**: <list>

No issues found above the confidence threshold (>= <threshold>).
<N> findings were filtered out below threshold; <V> were dropped at verification.
```

### 8. Hand off to fixing

Unless `--no-fix` was set (step 1), invoke the `reviewer:fix-findings` skill to apply fixes for the findings just reported; the verified findings are already in context.

### 9. Iterative re-review

If the user fixes issues and asks for re-review, repeat the process against the same scope as the original review, not just the flagged or fixed files - a narrowed re-review misses regressions and unfixed findings elsewhere in the scope. Narrow only when the user explicitly asks.
