# Teja's Plugin Marketplace

Personal collection of reusable Claude Code plugins — skills, agents, and hooks packaged for easy installation across projects.

## Available Plugins

| Plugin | Description |
|--------|-------------|
| [adr](plugins/adr/) | Architecture Decision Records - consult and manage ADRs for project design decisions |
| [skill-reviewer](plugins/skill-reviewer/) | Review SKILL.md files for quality, completeness, and best practices |

## Installation

Add this marketplace to your Claude Code settings, then install individual plugins:

```bash
claude plugin install teja-skills/adr
claude plugin install teja-skills/skill-reviewer
```

## Adding a New Plugin

1. Create a directory under `plugins/<plugin-name>/`
2. Add `.claude-plugin/plugin.json` with the plugin manifest
3. Add skills in `skills/<skill-name>/SKILL.md`
4. Register in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)
5. Update [`plugins/README.md`](plugins/README.md) with the new entry

See [AGENTS.md](AGENTS.md) for conventions, or consult the `plugins-guide` and `skills-guide` reference skills for detailed authoring rules.

## License

[MIT](LICENSE)
