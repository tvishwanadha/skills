# Teja's Plugin Marketplace

Personal collection of Claude Code plugins for reuse across projects.

## Plugins

| Name | Description | Contents |
|------|-------------|----------|
| [adr](./adr/) | Architecture Decision Records - consult and manage ADRs | 1 skill, 0 agents |
| [reviewer](./reviewer/) | Layered code review framework with extensible core reviews and parallel orchestration | 11 skills, 2 agents |
| [codex](./codex/) | Codex-powered code review, plan review, and completion verification | 2 skills, 1 agent, MCP server |
| [reviewer-extras](./reviewer-extras/) | Extra review types for the reviewer framework that depend on other plugins | 2 skills, 0 agents |

## Installation

Add this marketplace to your Claude Code settings, then install plugins:

```bash
claude plugin install teja-skills/adr
claude plugin install teja-skills/reviewer
claude plugin install teja-skills/codex
claude plugin install teja-skills/reviewer-extras
```

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
