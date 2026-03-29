---
name: codex-plugins-guide
description: >-
  This skill should be loaded when creating or modifying a Codex plugin
  manifest (.codex-plugin/plugin.json), registering a plugin in the Codex
  marketplace (.agents/plugins/marketplace.json), adding interface metadata,
  or troubleshooting Codex plugin discovery. Defines Codex-specific
  marketplace conventions for this repository.
user-invocable: false

---

# Codex Plugin Conventions

Conventions for Codex plugin manifests and marketplace registration in this repository.

For the full upstream spec, consult the official build guide at https://developers.openai.com/codex/plugins/build or the `$plugin-creator` skill bundled in Codex.

## Plugin Manifest

The manifest at `.codex-plugin/plugin.json` defines the plugin for Codex.

### Required fields

- `name` - must match the plugin directory name

### Recommended fields

- `version` - semver format
- `description` - concise summary
- `author` - object with `name`, `email`, optional `url`
- `license` - SPDX identifier

### Full manifest schema

```json
{
  "name": "plugin-name",
  "version": "1.2.0",
  "description": "Brief plugin description",
  "author": {
    "name": "Author Name",
    "email": "author@example.com",
    "url": "https://github.com/author"
  },
  "homepage": "https://docs.example.com/plugin",
  "repository": "https://github.com/author/plugin",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "skills": "./skills/",
  "mcpServers": "./.mcp.json",
  "apps": "./.app.json",
  "interface": { }
}
```

All fields use camelCase. Path fields (`skills`, `mcpServers`, `apps`) must start with `./` and stay within the plugin root - no `..` allowed. Default component discovery (`skills/`, etc.) works without declaring paths.

### Interface metadata

The optional `interface` block provides display metadata for the Plugin Directory:

```json
{
  "interface": {
    "displayName": "Plugin Display Name",
    "shortDescription": "Short description for subtitle",
    "longDescription": "Long description for details page",
    "developerName": "Developer Name",
    "category": "Productivity",
    "capabilities": ["Interactive", "Write"],
    "websiteURL": "https://example.com/",
    "privacyPolicyURL": "https://example.com/privacy",
    "termsOfServiceURL": "https://example.com/terms",
    "defaultPrompt": [
      "First starter prompt for the plugin.",
      "Second starter prompt for the plugin."
    ],
    "brandColor": "#3B82F6",
    "composerIcon": "./assets/icon.png",
    "logo": "./assets/logo.png",
    "screenshots": ["./assets/screenshot1.png"]
  }
}
```

`defaultPrompt` is an array of up to 3 strings, each capped at 128 characters.

Asset paths (`composerIcon`, `logo`, `screenshots`) must start with `./` and reference files inside the plugin directory. Absolute paths or paths outside the plugin root are silently ignored.

## Marketplace

### This repository

Register plugins in `.agents/plugins/marketplace.json`:

```json
{
  "name": "teja-skills",
  "interface": { "displayName": "Teja Skills" },
  "plugins": [
    {
      "name": "plugin-name",
      "source": { "source": "local", "path": "./plugins/plugin-name" },
      "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
      "category": "Developer Tools"
    }
  ]
}
```

The `codex` plugin is Claude Code-specific and excluded from this marketplace. To exclude any plugin from Codex, omit it from `.agents/plugins/marketplace.json`.

### Marketplace file format

**Top-level fields**: `name` (required), `interface.displayName` (optional). Plugin order in `plugins[]` determines render order in the Plugin Directory.

**Required per-plugin**: `name` and `source`.

- `source.source` is always `"local"`
- `source.path` must start with `./`, no `..` components

**Recommended per-plugin** (from upstream `$plugin-creator` conventions):

- `policy.installation`: `NOT_AVAILABLE`, `AVAILABLE` (default), `INSTALLED_BY_DEFAULT`
- `policy.authentication`: `ON_INSTALL` (default), `ON_USE`
- `category`: display category for the Plugin Directory

### Path resolution

The marketplace file lives at `<root>/.agents/plugins/marketplace.json`. Source paths resolve relative to `<root>` (two directories up from the marketplace file). So `"./plugins/adr"` resolves to `<root>/plugins/adr`.

This applies to both repo marketplaces (`<repo-root>/.agents/plugins/marketplace.json`) and home marketplaces (`~/.agents/plugins/marketplace.json`).

## Plugin Components

### Skills

Skills within plugins live in `skills/<skill-name>/SKILL.md` and follow the same conventions as local skills. For skill authoring guidance, consult `skills-guide`.

### Agents

Codex plugins cannot contribute subagent definitions. Subagents must be added manually as TOML files in `.codex/agents/` at the project root or `~/.codex/agents/` for personal agents. See the [Codex subagents guide](https://developers.openai.com/codex/subagents) for the TOML schema and options.

If a plugin relies on custom agents, document the required TOML definitions in the plugin README so users can create them manually.

### Apps

App connector mappings in `.app.json` connect plugins to external services (GitHub, Slack, etc.).

### MCP servers

MCP server configuration in `.mcp.json` gives the plugin access to additional tools.

## Reference

- `openai/plugins` repo: canonical marketplace example with `interface` metadata
- Official docs: https://developers.openai.com/codex/plugins
- Build guide: https://developers.openai.com/codex/plugins/build
