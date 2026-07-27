# Agent Guide

This repository is a personal plugin marketplace - a collection of reusable skills, agents, and plugins for AI coding assistants.

## Directory Layout

```
├── plugins/                          # Published plugins
│   └── <plugin-name>/
│       ├── .claude-plugin/plugin.json  # Claude manifest
│       ├── .codex-plugin/plugin.json   # Codex manifest
│       ├── skills/
│       └── agents/
├── .claude/skills/                   # Local skills (guides, review overrides, custom review types)
├── .codex/agents/                    # Codex subagent definitions (TOML)
├── .codex/config.toml                # Codex project config
├── .claude-plugin/marketplace.json   # Claude marketplace registry
└── .agents/
    ├── plugins/marketplace.json      # Codex marketplace registry
    └── skills/<name>                 # symlink to ../../.claude/skills/<name>, exposes local skills to Codex
```

Enabled plugins are configured in `.claude/settings.json` - only reference plugins listed there. These load from the remote/cached version, not the working copy on disk. To test local plugin edits, stop and ask the user to restart with `--plugin-dir` pointing to the local copy.

## Guide Skills

**Always consult the relevant guide skill before making changes.** These define the conventions and quality bar for this repository.

| Skill | When to consult |
|-------|-----------------|
| `skills-guide` | Creating or modifying SKILL.md files, skill authoring conventions and voice; the full field spec lives in the reviewer plugin's default-skill checklist |
| `claude-plugins-guide` | Claude Code marketplace registration, `.claude-plugin/plugin.json` manifest, agent and skill conventions within plugins |
| `codex-plugins-guide` | Codex marketplace registration, `.codex-plugin/plugin.json` manifest, `interface` metadata, path resolution, `.codex/agents/*.toml` role definitions |

## Delegation

Delegate implementation to the cascade `implementer` role and command execution to the cascade `mechanic` role - the top-level session plans, reviews, and decides; it does not write code or run builds and tests itself. Reserve direct shell use for trivial read-only one-liners.

When a plan converges - in plan mode or in conversation - hand execution to the `orchestrate` skill.

## Conventions

- Use plain hyphens (`-`), never em dashes, in all markdown files.
- The Codex role files (`.codex/agents/*.toml`) mirror the corresponding agent definitions in `plugins/*/agents/` and must change together, keeping the behavioral contract identical.

## Testing

Run `reviewer:self-review --diff main` before finishing substantial changes.
