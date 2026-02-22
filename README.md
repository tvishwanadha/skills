# Teja's Plugin Marketplace

Personal collection of Claude Code plugins for reuse across projects.

## Plugins

| Name | Description | Contents |
|------|-------------|----------|
| [adr](plugins/adr/) | Architecture Decision Records - consult and manage ADRs | 1 skill, 0 agents |
| [reviewer](plugins/reviewer/) | Layered code review framework with extensible core reviews and parallel orchestration | 11 skills, 2 agents |
| [codex](plugins/codex/) | Codex-powered code review, plan review, and completion verification | 2 skills, 1 agent, MCP server |
| [reviewer-extras](plugins/reviewer-extras/) | Extra review types for the reviewer framework that depend on other plugins | 2 skills, 0 agents |
| [planner](plugins/planner/) | Enhanced planning with complexity scoring, sub-problem decomposition, and plan review | 3 skills, 0 agents |

## Installation

Add this marketplace to your Claude Code settings, then install plugins:

```bash
claude plugin install teja-skills/adr
claude plugin install teja-skills/reviewer
claude plugin install teja-skills/codex
claude plugin install teja-skills/reviewer-extras
claude plugin install teja-skills/planner
```

## Adding a New Plugin

1. Create a directory under `plugins/<plugin-name>/`
2. Add `.claude-plugin/plugin.json` with the plugin manifest
3. Add skills in `skills/<skill-name>/SKILL.md`
4. Register in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)
5. Update the plugin table above with the new entry

See [AGENTS.md](AGENTS.md) for conventions, or consult the `plugins-guide` and `skills-guide` reference skills for detailed authoring rules.

## Plugin Structure

Each plugin follows the standard Claude Code plugin structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── skills/                  # Agent Skills
│   └── skill-name/
│       └── SKILL.md         # Skill definition
├── agents/                  # Agent definitions (optional)
│   └── agent-name.md
└── README.md                # Plugin documentation (optional)
```

## License

[MIT](LICENSE)
