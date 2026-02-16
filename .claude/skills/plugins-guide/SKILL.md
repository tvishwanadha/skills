---
name: plugins-guide
description: >-
  Plugin structure and packaging conventions. Consult when creating or modifying
  plugins, writing plugin.json manifests, or registering plugins in the marketplace.
user-invocable: false

---

# Plugin Structure and Packaging Guide

Reference for structuring Claude Code plugins in this marketplace.

## Plugin Directory Layout

Each plugin lives under `plugins/<plugin-name>/` and follows this structure:

```
plugins/<plugin-name>/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata and manifest
├── skills/                  # Skills provided by this plugin
│   └── <skill-name>/
│       └── SKILL.md         # Skill definition
├── agents/                  # Agent definitions (optional)
│   └── <agent-name>.md
├── hooks/                   # Hook scripts (optional)
└── README.md                # Plugin documentation (optional)
```

## plugin.json Schema

The manifest file at `.claude-plugin/plugin.json` defines the plugin:

```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Short description of what the plugin does",
  "author": {
    "name": "Author Name",
    "email": "author@example.com"
  },
  "license": "MIT",
  "skills": "./skills/",
  "agents": "./agents/",
  "hooks": "./hooks/"
}
```

### Required fields
- `name` - lowercase, hyphen-separated identifier
- `version` - semver format
- `description` - concise summary of the plugin's purpose

### Optional fields
- `author` - object with `name` and `email`
- `license` - SPDX license identifier
- `skills` - path to skills directory (relative to plugin root)
- `agents` - path to agents directory (relative to plugin root)
- `hooks` - path to hooks directory

This guide covers common fields. For unrecognized fields, check the upstream references before flagging as an issue.

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

Required per-plugin fields: `name` and `source`. `description` is optional but recommended. The marketplace file lives at the repository root.

## Skills Within Plugins

Skills bundled in a plugin follow the same conventions as standalone skills. See the [`skills-guide`](../skills-guide/SKILL.md) for detailed SKILL.md authoring rules.

Key points:
- One SKILL.md per skill, in its own directory under `skills/`
- Skill directory name must match the `name` field in frontmatter
- Plugin skills are installed via `claude plugin install <marketplace>/<plugin-name>`

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

The `tools` field is optional. If omitted, the agent inherits all tools from the parent conversation, including MCP tools. Specify `tools` only to restrict the agent to a subset of tools.

## Naming Conventions

- **Plugin names**: lowercase, hyphen-separated (e.g., `skill-reviewer`, `adr`)
- **Skill names**: match containing directory, lowercase with hyphens
- **Agent names**: match filename (without `.md`), lowercase with hyphens

## Upstream References

For edge cases or to verify specific rules, consult:
1. The `claude-code-guide` agent (if available in the current environment)
2. Upstream documentation:
   - `https://code.claude.com/docs/en/plugins.md`
   - `https://code.claude.com/docs/en/plugins-reference.md`
   - `https://code.claude.com/docs/en/plugin-marketplaces.md`
