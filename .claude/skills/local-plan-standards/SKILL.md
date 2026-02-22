---
name: local-plan-standards
description: Project-specific planning constraints and plan review rules.
user-invocable: false
---

# Local Plan Standards

## Constraints

- Plugin and skill changes must include marketplace registration (`.claude-plugin/marketplace.json`) and catalog updates (`plugins/README.md`)
- Skills must follow `skills-guide` conventions: frontmatter fields, naming, content structure, size limits
- Plugins must follow `plugins-guide` conventions: manifest, directory layout, component organization
- References to existing plugins or skills must resolve on disk - flag non-existent components unless they are being created as part of the plan
- READMEs should summarize behavior at a high level, not duplicate detail from SKILL.md - avoid creating two sources of truth that can drift
- Plans should include a verification step (e.g., `self-review --diff main`) to validate changes against conventions

## Review process

During plan review (Phase 5), apply the constraints above as a checklist. Additionally:

- Load `skills-guide` and `plugins-guide` via the Skill tool to verify specific conventions when the plan touches skills or plugins
- Verify referenced file paths with Glob

### Codex holistic review

In parallel with the rule-based review, launch `reviewer-extras:review-codex` assigned to the `codex:review` agent to review the plan for gaps, missed edge cases, and overall coherence. Coalesce findings from both into a single report.

Skip Codex review for plans with complexity below 3.
