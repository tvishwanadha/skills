---
name: plugins-guide
description: >-
  This skill should be loaded when creating a plugin, modifying a plugin
  manifest, writing plugin.json, registering a plugin in the marketplace, or
  adding skills or agents to a plugin. Defines marketplace registration and
  plugin conventions for this repository.
user-invocable: false

---

# Plugin Conventions - Marketplace Reference

Marketplace-specific conventions for plugins in this repository. For generic Claude Code plugin structure guidance, see the wrapping note at the end.

## plugin.json Schema

The manifest file at `.claude-plugin/plugin.json` defines the plugin:

### Required fields
- `name` - lowercase, hyphen-separated identifier
- `version` - semver format. Start at `0.0.1`. Under `0.x`, default to patch bumps unless stated otherwise
- `description` - concise summary of the plugin's purpose

### Optional fields
- `author` - object with `name` and `email`
- `license` - SPDX license identifier
- `skills` - custom path to skills directory (only needed if not using default `skills/`)
- `hooks` - path to hooks directory
- `mcpServers` - MCP server configuration

Skills, agents, and commands in their default directories (`skills/`, `agents/`, `commands/`) are auto-discovered - no manifest field needed. Only declare paths for non-standard locations.

The `plugin-structure` skill from plugin-dev has the full manifest reference including all supported fields.

## Marketplace Registration

To add a plugin to this marketplace, add an entry to [`.claude-plugin/marketplace.json`](../../../.claude-plugin/marketplace.json):

Add a new entry to the `plugins` array in the marketplace file:

```json
{
  "name": "plugin-name",
  "source": "./plugins/plugin-name",
  "description": "Short description"
}
```

Required per-plugin fields: `name` and `source`. `description` is optional but recommended. The marketplace file lives at `.claude-plugin/marketplace.json` in the repository.

## Agents Within Plugins

Agent definitions are Markdown files that configure a subagent:

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

For comprehensive agent conventions (frontmatter fields, triggering conditions, system prompts, model selection), consult the `plugin-dev:agent-development` skill.

## Skills Within Plugins

Skills within plugins follow the same conventions as local skills. For skill authoring guidance (frontmatter, content structure, review criteria), consult `skills-guide` which references `plugin-dev:skill-development` for the full specification.

## Generic Plugin Conventions

For comprehensive Claude Code plugin structure guidance (directory layout, auto-discovery, `${CLAUDE_PLUGIN_ROOT}`, component patterns, naming conventions), consult the `plugin-dev:plugin-structure` skill if plugin-dev is installed.
