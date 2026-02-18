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

- `review-plugin` (local skill) assigned to `reviewer:reviewer` (opus - structural analysis of plugin directories)
- `reviewer-extras:review-codex` (plugin skill) assigned to `codex:review` (has Codex MCP tools for deep code review)

## Adjust confidence threshold

- Lower confidence threshold to >= 70 (structural checks and Codex-translated findings may have slightly lower confidence than built-in review types)

## Pre-review step: validate marketplace registry

Before launching review tasks, run these pre-flight checks:

1. Read `.claude-plugin/marketplace.json` and parse the `plugins` array
2. For each registered plugin, verify the `source` path resolves to an existing directory (use Glob)
3. Glob for `plugins/*/` directories and check each against the registry - flag any unregistered plugin directories
4. Flag any registry entries whose source path does not exist on disk (orphaned entries)
5. Report pre-flight findings before launching the parallel review tasks
