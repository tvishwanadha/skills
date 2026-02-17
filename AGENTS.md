# Agent Guide

This repository is a personal plugin marketplace - a collection of reusable skills, agents, and plugins for AI coding assistants.

## Directory Layout

```
├── plugins/                        # Published plugins (one directory per plugin)
│   ├── README.md                   # Plugin catalog
│   └── <plugin-name>/
│       ├── .claude-plugin/plugin.json
│       ├── skills/
│       └── agents/
├── .claude/
│   ├── skills/                     # Local skills (guide skills, review overrides, custom review types)
│   │   ├── plugins-guide/          # Plugin conventions (guide skill)
│   │   ├── skills-guide/           # Skill conventions (guide skill)
│   │   ├── local-review-skill/     # Local override for skill review
│   │   ├── local-review-patterns/  # Local override for patterns review
│   │   ├── local-review-documentation/ # Local override for documentation review
│   │   ├── review-plugin/          # Plugin structure review type
│   │   ├── review-codex/           # Codex-powered deep review type
│   │   └── self-review-extension/  # Self-review orchestrator extension
│   ├── agents/                     # Local agents (optional, created on demand)
│   └── settings.json               # Project-wide plugin configuration
├── .claude-plugin/
│   └── marketplace.json            # Marketplace registry
├── CLAUDE.md                       # Project instructions
├── AGENTS.md                       # This file - agent guidance
├── README.md                       # Marketplace overview
└── LICENSE
```

## Installed Plugins

Three plugins are enabled project-wide via `.claude/settings.json`:

- **`plugin-dev`** (external) - generic Claude Code plugin, skill, and agent development conventions
- **`reviewer`** (this repository) - layered code review framework with extensible core reviews and parallel orchestration
- **`codex`** (this repository) - Codex-powered code review, plan review, and completion verification via MCP

**Important**: enabled plugins use the distributed (installed) version, not the working copy on disk. If you edit plugin files locally, those changes will not take effect until Claude Code is restarted. If you need to test local plugin changes, stop and ask the user to restart.

## Guide Skills

**Always consult the relevant guide skill before making changes.** These define the conventions and quality bar for this repository.

| Skill | When to consult |
|-------|-----------------|
| `skills-guide` | Creating or modifying SKILL.md files, reviewing skill quality, understanding frontmatter fields |
| `plugins-guide` | Marketplace registration, plugin manifest conventions, agent and skill conventions within plugins |

## Local Skill Naming Conventions

- `*-guide` - guide skills (conventions and quality bar)
- `review-*` - custom review types (e.g., `review-plugin`, `review-codex`)
- `local-review-*` - overrides for built-in review types (e.g., `local-review-skill`)
- `self-review-extension` - orchestrator customization

## Quality Bar

Local skills follow `skills-guide` conventions. Local agents follow `plugin-dev:agent-development` conventions. Plugins follow `plugins-guide` (which references both). Good contributions are concise, well-structured, and follow existing patterns.

## Marketplace Registration

When adding a new plugin, register it in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) and update [`plugins/README.md`](plugins/README.md). The marketplace file maps plugin names to their source directories.

## Testing

Run `/reviewer:self-review` to validate plugins, local skills, and documentation across the repository. The local extension adds plugin-specific and Codex-powered reviews on top of the built-in review types.

```
/reviewer:self-review                   # full review
/reviewer:self-review --diff main       # review changes vs main branch
/review-plugin plugins/codex            # plugin structure review (local custom type)
```

## Upstream References

Generic Claude Code conventions (plugin structure, skill authoring, hooks, MCP) come from `plugin-dev` skills. For edge cases beyond what local guide skills cover, consult the relevant plugin-dev skill or the `claude-code-guide` agent.
