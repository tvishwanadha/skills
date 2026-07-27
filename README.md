# Teja's Plugin Marketplace

Personal collection of AI coding assistant plugins for reuse across projects. Compatible with both Claude Code and OpenAI Codex.

## Plugins

| Name | Description |
|------|-------------|
| [adr](plugins/adr/) | Architecture Decision Records - consult and manage ADRs |
| [reviewer](plugins/reviewer/) | Layered code review framework with extensible core reviews and parallel orchestration |
| [codex](plugins/codex/) | Codex-powered code review, plan review, and completion verification |
| [reviewer-extras](plugins/reviewer-extras/) | Extra review types for the reviewer framework that depend on other plugins |
| [cascade](plugins/cascade/) | Multi-model orchestration hierarchy - the session orchestrates and signs off, a lead plans and delivers each vertical slice, an implementer writes the code, a mechanic runs commands |

## Installation

### Claude Code

Add this marketplace to your Claude Code settings, then install plugins:

```bash
claude plugin install teja-skills/adr
claude plugin install teja-skills/reviewer
claude plugin install teja-skills/codex
claude plugin install teja-skills/reviewer-extras
claude plugin install teja-skills/cascade
```

### Codex

Add one entry per plugin to a project marketplace at `<your-repo>/.agents/plugins/marketplace.json`:

```json
{
  "name": "project-plugins",
  "interface": { "displayName": "Project Plugins" },
  "plugins": [
    {
      "name": "reviewer",
      "source": {
        "source": "git-subdir",
        "url": "https://github.com/tvishwanadha/skills.git",
        "path": "./plugins/reviewer",
        "ref": "main"
      },
      "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
      "category": "Developer Tools"
    }
  ]
}
```

Register the marketplace, then install:

```bash
codex plugin marketplace add .
codex plugin add reviewer@project-plugins
```

- A repo marketplace is not discovered on its own - `codex plugin marketplace add .` is required. Only a personal marketplace at `~/.agents/plugins/marketplace.json` is picked up implicitly.
- Registration and enablement live in `$CODEX_HOME/config.toml`, so the checked-in marketplace file is a shared catalog rather than an auto-install - each person still runs both commands.
- Plugins that depend on other plugins need an entry for each dependency. Claude plugins install on Codex too - a `.codex-plugin/plugin.json` is not required.
- Codex plugins cannot contribute subagents. `reviewer` and `cascade` need their role definitions copied by hand - see their READMEs.

For personal use, `codex plugin marketplace add https://github.com/tvishwanadha/skills.git` registers this repo's own marketplace directly, skipping the project file - but it exposes only the plugins listed here.

## Adding a New Plugin

1. Create a directory under `plugins/<plugin-name>/`
2. Add `.claude-plugin/plugin.json` with the plugin manifest
3. Add skills in `skills/<skill-name>/SKILL.md`
4. Register in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)
5. Add `.codex-plugin/plugin.json` manifest (copy version/description/author/license from Claude manifest, add `interface` block) - Claude-specific plugins skip steps 5 and 6
6. Register in [`.agents/plugins/marketplace.json`](.agents/plugins/marketplace.json) with `policy` and `category`
7. Update the plugin table above with the new entry

See [AGENTS.md](AGENTS.md) for conventions, or consult the `claude-plugins-guide`, `codex-plugins-guide`, and `skills-guide` reference skills for detailed authoring rules.

## Plugin Structure

Each plugin follows the standard plugin structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Claude Code manifest
├── .codex-plugin/
│   └── plugin.json          # Codex manifest (omitted by Claude-specific plugins)
├── skills/                  # Agent Skills
│   └── skill-name/
│       └── SKILL.md         # Skill definition
├── agents/                  # Agent definitions (optional)
│   └── agent-name.md
└── README.md                # Plugin documentation (optional)
```

## Testing

```
/reviewer:self-review                   # full review
/reviewer:self-review --diff main       # review changes vs main branch
```

## License

[MIT](LICENSE)
