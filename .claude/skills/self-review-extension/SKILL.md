---
name: self-review-extension
description: >-
  Extend self-review with plugin structure and Codex review types, lower
  confidence threshold to 70, and add marketplace registry pre-flight checks.
user-invocable: false
---

# Self-Review Extension

Modify the default self-review configuration:

## Add review types

- `review-plugin` (local skill) assigned to `reviewer:reviewer`
- `reviewer-extras:review-codex` (plugin skill) assigned to `codex:review`. **Skip if the `codex:review` skill is not installed.**
- `reviewer-extras:review-claude-md` (plugin skill) assigned to `reviewer:reviewer`

## Adjust confidence threshold

- Lower confidence threshold to >= 70 (structural checks and Codex-translated findings may have slightly lower confidence than built-in review types)

## Pre-review step: validate marketplace registry

Before launching review tasks, run these pre-flight checks:

1. Read `.claude-plugin/marketplace.json` and parse the `plugins` array
2. For each registered plugin, verify the `source` path resolves to an existing directory on disk
3. Read `.agents/plugins/marketplace.json` and parse the `plugins` array
4. For each registered Codex plugin, verify the `source.path` resolves to an existing directory on disk
5. Search on disk for `plugins/*/` directories and check each against both registries - flag any unregistered plugin directories (the `codex` plugin is Claude-specific and should only appear in the Claude registry)
6. Flag any registry entries whose source path does not exist on disk (orphaned entries)
7. Cross-check that plugins registered in both marketplaces have consistent `name` fields
8. Report pre-flight findings before launching the parallel review tasks
