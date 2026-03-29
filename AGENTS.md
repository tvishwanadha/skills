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
├── .claude-plugin/marketplace.json   # Claude marketplace registry
└── .agents/plugins/marketplace.json  # Codex marketplace registry
```

Enabled plugins are configured in `.claude/settings.json` - only reference plugins listed there. These load from the remote/cached version, not the working copy on disk. To test local plugin edits, stop and ask the user to restart with `--plugin-dir` pointing to the local copy.

## Guide Skills

**Always consult the relevant guide skill before making changes.** These define the conventions and quality bar for this repository.

| Skill | When to consult |
|-------|-----------------|
| `skills-guide` | Creating or modifying SKILL.md files, reviewing skill quality, understanding frontmatter fields |
| `claude-plugins-guide` | Claude Code marketplace registration, `.claude-plugin/plugin.json` manifest, agent and skill conventions within plugins |
| `codex-plugins-guide` | Codex marketplace registration, `.codex-plugin/plugin.json` manifest, `interface` metadata, path resolution |

## Testing

Run `/reviewer:self-review --diff main` before finishing substantial changes.
