---
name: customize-plan-review
description: >-
  This skill should be used when the user asks to "customize plan review",
  "add planning constraints", "override plan review defaults", "set up
  local-plan-standards", or "configure plan review rules". Create or modify
  a local-plan-standards skill for project-specific planning constraints.
allowed-tools: Read, Write, Edit, Glob
---

# Customize Plan Review

Create or modify a `local-plan-standards` skill that adds project-specific planning constraints and review rules.

## Procedure

### 1. Show current defaults

Read the plugin's default plan review rules:

- `${CLAUDE_PLUGIN_ROOT}/skills/review-plan/references/default-plan-review.md`

Present a summary so the user understands what they're extending or replacing.

### 2. Choose mode

Ask the user:
- **Extend** - add project-specific constraints on top of the defaults
- **Override** - replace defaults entirely with a custom ruleset

### 3. Check for existing local skill

Use Glob to check if `.claude/skills/local-plan-standards/SKILL.md` already exists.

If it exists:
1. Read the file and present its current content
2. Ask: **replace** (overwrite), **merge** (use Edit to append new constraints), or **cancel**
3. If cancel, stop here

### 4. Discover available skills

Scan for skills the user might want to reference as planning constraints:
- Glob for `.claude/skills/*/SKILL.md` (local skills)
- Read `.claude/settings.json` for enabled plugin skills

Present discovered skills as candidates. Planning constraints often reference convention skills (e.g., "Code must follow `local-review-patterns` conventions") - the planner loads referenced skills during Phase 1 to understand the full constraint.

### 5. Gather planning constraints

Ask the user for project-specific planning constraints. Guide them to provide concrete, actionable rules. Each constraint can reference a skill that defines its detailed rules.

Examples:
- "All database changes must include a migration plan"
- "Code must follow `local-review-patterns` conventions"
- "Plans touching the API layer must include backwards-compatibility analysis"

### 6. Gather review process additions

Ask the user for review process rules, or offer a sensible default:

> "Apply the constraints above as a checklist during plan review."

### 7. Generate and write

Read the template from [assets/local-plan-standards.md](assets/local-plan-standards.md). Replace placeholders:

| Placeholder | Value |
|-------------|-------|
| `{MODE_LINE}` | "These rules extend the default plan review rules." or "These rules replace the default plan review rules. Do NOT load defaults." |
| `{CONSTRAINTS}` | User-provided bullet list of constraints |
| `{REVIEW_PROCESS}` | User-provided review process additions |

Write the result to `.claude/skills/local-plan-standards/SKILL.md`.

### 8. Confirm

Print what was created and how it integrates:
- `review-plan` automatically loads `local-plan-standards` during plan review
- `planning-methodology` Phase 1 loads it for planning constraints
- Override mode is detected by the "Do NOT load defaults" line
