---
name: claude-plugins-guide
description: >-
  This skill should be loaded when creating a plugin, modifying a Claude Code
  plugin manifest (.claude-plugin/plugin.json), registering a plugin in the
  Claude marketplace, or adding skills or agents to a plugin. Defines Claude
  Code marketplace registration and plugin conventions for this repository.
user-invocable: false

---

# Claude Code Plugin Conventions

Conventions for Claude Code plugin manifests and marketplace registration in this repository.

For comprehensive plugin structure guidance (directory layout, auto-discovery, `${CLAUDE_PLUGIN_ROOT}`, component patterns), consult `plugin-dev:plugin-structure` if available.

## Plugin Manifest

The manifest at `.claude-plugin/plugin.json` defines the plugin for Claude Code.

### Required fields

- `name` - lowercase, hyphen-separated identifier
- `version` - semver format, start at `0.0.1`
- `description` - concise summary of the plugin's purpose

### Recommended fields

- `author` - object with `name` and `email`
- `license` - SPDX identifier
- `skills` - custom path to skills directory (only needed if not using default `skills/`)
- `hooks` - path to hooks directory
- `mcpServers` - MCP server configuration

Skills, agents, and commands in their default directories (`skills/`, `agents/`, `commands/`) are auto-discovered - no manifest field needed. Only declare paths for non-standard locations.

## Marketplace

### This repository

Add an entry to the `plugins` array in [`.claude-plugin/marketplace.json`](../../../.claude-plugin/marketplace.json):

```json
{
  "name": "plugin-name",
  "source": "./plugins/plugin-name",
  "description": "Short description"
}
```

**Required per-plugin**: `name` and `source`. `description` is optional but recommended.

### Path resolution

The marketplace file lives at `<repo-root>/.claude-plugin/marketplace.json`. Source paths are relative to the repo root.

## Plugin Components

### Skills

Skills within plugins follow the same conventions as local skills. Each skill lives in `skills/<skill-name>/SKILL.md`. For skill authoring guidance, consult `skills-guide` or `plugin-dev:skill-development` if available.

### Agents

Agent definitions are Markdown files in `agents/`:

```yaml
---
name: agent-name
description: What the agent does
tools: Read, Glob, Grep
skills:
  - skill-name
---

System prompt / instructions for the agent.
```

For comprehensive agent conventions (frontmatter fields, triggering conditions, model selection), consult `plugin-dev:agent-development` if available.

### Hooks

Hook definitions in `hooks/` provide event-driven automation. For hook conventions, consult `plugin-dev:hook-development` if available.

## Reference

- `plugin-dev:plugin-structure` - full manifest reference and directory layout conventions
- `plugin-dev:agent-development` - agent frontmatter, tools, and triggering
- `plugin-dev:skill-development` - skill authoring spec
- `plugin-dev:hook-development` - hook events and configuration
