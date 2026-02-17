---
name: local-review-skill
description: Project-specific skill review rules.
user-invocable: false
---

# Local Review: skill

These rules extend the default skill review rules.

## Rules

- Consult the `skills-guide` skill via the Skill tool for marketplace-specific skill conventions before reviewing
- Do not flag missing `disable-model-invocation` on orchestration or review skills (e.g., `self-review`, `review-*`) - auto-invocation is intentional for these skill types in this repository
- Skip SKILL.md files under `plugins/*/` directories - those are reviewed by `review-plugin` as part of the plugin review (avoids double-review)
