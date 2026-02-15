# Teja's Plugin Marketplace

Personal collection of Claude Code plugins for reuse across projects.

## Plugins

| Name | Description | Contents |
|------|-------------|----------|
| [adr](./adr/) | Architecture Decision Records - consult and manage ADRs for project design decisions | **Skill:** `adr` - Auto-invoked when planning features, modifying core systems, or making architectural decisions. Includes reference guide on ADR best practices. |

## Installation

Add this marketplace to your Claude Code settings, then install plugins:

```bash
claude plugin install teja-skills/adr
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
└── README.md                # Plugin documentation (optional)
```
