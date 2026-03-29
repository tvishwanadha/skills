# Teja's Plugin Marketplace

Personal collection of AI coding assistant plugins for reuse across projects. Compatible with both Claude Code and OpenAI Codex.

## Plugins

| Name | Description |
|------|-------------|
| [adr](plugins/adr/) | Architecture Decision Records - consult and manage ADRs |
| [reviewer](plugins/reviewer/) | Layered code review framework with extensible core reviews and parallel orchestration |
| [codex](plugins/codex/) | Codex-powered code review, plan review, and completion verification |
| [reviewer-extras](plugins/reviewer-extras/) | Extra review types for the reviewer framework that depend on other plugins |
| [planner](plugins/planner/) | Enhanced planning with complexity scoring, sub-problem decomposition, and plan review |

## Installation

### Claude Code

Add this marketplace to your Claude Code settings, then install plugins:

```bash
claude plugin install teja-skills/adr
claude plugin install teja-skills/reviewer
claude plugin install teja-skills/codex
claude plugin install teja-skills/reviewer-extras
claude plugin install teja-skills/planner
```

### Codex

All plugins except `codex` (which is Claude-specific) are also available via the Codex marketplace at `.agents/plugins/marketplace.json`.

## Adding a New Plugin

1. Create a directory under `plugins/<plugin-name>/`
2. Add `.claude-plugin/plugin.json` with the plugin manifest
3. Add skills in `skills/<skill-name>/SKILL.md`
4. Register in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)
5. Add `.codex-plugin/plugin.json` manifest (copy version/description/author/license from Claude manifest, add `interface` block)
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
│   └── plugin.json          # Codex manifest
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
