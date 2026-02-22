---
name: review-plan
description: >-
  Review implementation plans for completeness, feasibility, risk coverage,
  architectural alignment, and coherence. Use when asked to "review plan",
  "check plan", "validate plan", or before exiting plan mode.
allowed-tools: Read, Glob, Grep, Skill
argument-hint: "[plan file path]"
---

# Review: Plan

Review an implementation plan for completeness, feasibility, and coherence.

**Input**: `$ARGUMENTS` - path to a plan file. If no argument, find and review the most recent `.md` file in `.claude/plans/`.

## Examples

- `/planner:review-plan .claude/plans/my-plan.md` - review a specific plan
- `/planner:review-plan` - review the most recent plan file in `.claude/plans/`

## Loading Strategy

1. Try to invoke the skill `local-review-plan` using the Skill tool.
   - If it loads and its instructions say to NOT use the defaults, use only the local skill's guidance. Skip step 2.
   - If it loads and does NOT prohibit defaults, continue to step 2, combining the local guidance with the defaults.
   - If it does not load (skill not found), continue to step 2.

2. Read the default rules from [references/default-plan-review.md](references/default-plan-review.md).

## Review Procedure

1. **Determine scope** from `$ARGUMENTS`
   - If a file path is provided, read that plan file
   - If no argument, use Glob to find the most recent `.md` file in `.claude/plans/` and read it

2. **Read the plan** and understand its structure, goals, and proposed changes

3. **Load output format** - invoke `reviewer:reviewer-framework` via the Skill tool for severity levels, confidence scoring, and output format. If it does not load, use this format:
   ```
   [SEVERITY] Category: Brief description (confidence: N)
     Section: <plan section or file reference>
     Issue: What's wrong and why it matters
     Suggestion: How to fix
   ```
   Severity: critical (plan won't work), high (significant gap), medium (could be better), low (minor). Confidence: 0-100, score conservatively. End with: `Total: N findings (X critical, Y high, Z medium, W low)`

4. **Apply loaded review rules** - check each category from the loaded guidance (defaults, local, or combined)

5. **Verify claims against the codebase**:
   - Use Glob to confirm referenced files and directories exist
   - Use Grep to verify referenced functions, classes, and patterns are real
   - Use Read to check that proposed modifications are compatible with existing code

6. **Report findings** using the loaded or built-in format
