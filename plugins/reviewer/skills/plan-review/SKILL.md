---
name: plan-review
description: >-
  Review implementation plans for completeness, feasibility, and correctness. Use
  before exiting plan mode or when asked to review a plan.
allowed-tools: Read, Glob, Grep, Task, Skill
argument-hint: "[path-to-plan]"
---

# Plan Review

Review an implementation plan for completeness, feasibility, correctness, and alignment with project conventions. Launches parallel review agents focused on different aspects of plan quality.

## Examples

- `/plan-review` - review the current plan file
- `/plan-review path/to/plan.md` - review a specific plan file

## Built-in Defaults

These defaults apply unless overridden by a `plan-review-extension` skill:

- **Review types**:
  - `review-logic` - logical consistency, edge cases, feasibility of proposed approach
  - `review-patterns` - alignment with project conventions and patterns
  - `review-documentation` - plan structure, clarity, completeness
- **Agent**: `reviewer:reviewer` (opus) for all review types
- **Confidence threshold**: >= 80

## Procedure

> **All Task calls must be foreground** - never set `run_in_background: true`. Subagents need the Skill tool for conditional loading, which is only available in foreground tasks.

### 1. Locate the plan

From `$ARGUMENTS`:
- If a path is given, read the file at that path
- If no argument, look for the current plan file (check for common locations: `plan.md`, `.claude/plan.md`, or ask the user)

### 2. Read plan content

Read the plan file and understand its scope - what it proposes to change, create, or modify.

### 3. Check for extension

Try to invoke `plan-review-extension` via the Skill tool.

If it loads, its instructions take priority over the built-in defaults. The extension can:
- Add or remove review types
- Change agent assignments per review type
- Add plan-specific checks (e.g., architectural review, security review)
- Adjust the confidence threshold

If it does not load (skill not found), use the built-in defaults.

### 4. Launch parallel reviews

For each review type in the final list, launch a Task:

- **`subagent_type`**: the agent name from defaults/extension (default: `"reviewer"` which maps to `reviewer:reviewer`)
- **`prompt`**: instruct the agent to invoke the corresponding review skill with the plan file as scope. Include the plan content in the prompt so the reviewer has full context.

Launch all Tasks in a single message for parallel execution. Do NOT use `run_in_background: true`.

Example Task prompt:
```
Invoke the skill `reviewer:review-logic` with argument: path/to/plan.md

You are reviewing an implementation plan, not code. Focus on:
- Logical consistency of the proposed approach
- Edge cases or scenarios the plan doesn't address
- Feasibility of the implementation steps
- Dependencies between steps that could cause issues

Plan content:
<plan content here>
```

### 5. Coalesce findings

After all Tasks complete, combine their outputs:

1. Collect all findings from all review types
2. Deduplicate overlapping findings (keep highest confidence)
3. Filter by confidence threshold (>= 80, or as adjusted by extension)
4. Sort by severity (critical > high > medium > low), then by confidence (descending)

### 6. Present report

```
# Plan Review Report

**Plan**: <plan file path>
**Review types**: <list of review types run>

## Findings

[SEVERITY] Category: Brief description (confidence: N)
  Section: <plan section or step reference>
  Details: What's wrong and why it matters
  Suggestion: Specific improvement
  Found by: <review-type>

...

## Summary

Total: N findings (X critical, Y high, Z medium, W low)
```

If no findings, report the plan as clean:

```
# Plan Review Report

**Plan**: <plan file path>
**Review types**: <list>

No issues found. The plan looks complete and well-structured.
```
