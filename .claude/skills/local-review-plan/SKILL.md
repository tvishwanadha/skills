---
name: local-review-plan
description: Project-specific plan review rules.
user-invocable: false
---

# Local Review: plan

These rules extend the default plan review rules.

## Rules

- If the plan creates or modifies plugins/skills, verify it includes marketplace registration (`.claude-plugin/marketplace.json`) and `plugins/README.md` updates
- Consult the `skills-guide` skill via the Skill tool for skill naming, frontmatter, and content conventions
- Consult the `plugins-guide` skill via the Skill tool for plugin structure, manifest, and marketplace conventions
- If the plan references existing plugins or skills, use Glob to verify they exist on disk - flag references to non-existent components unless they are being created as part of the plan

## Codex holistic review

In parallel with the rule-based review above, launch `reviewer-extras:review-codex` assigned to the `codex:review` agent (which has Codex MCP tools) to review the plan file for gaps, missed edge cases, and overall coherence. Coalesce findings from both the rule-based review and the Codex review into a single report.
