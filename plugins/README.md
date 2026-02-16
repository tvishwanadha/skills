# Teja's Plugin Marketplace

Personal collection of Claude Code plugins for reuse across projects.

## Plugins

| Name | Description | Contents |
|------|-------------|----------|
| [adr](./adr/) | Architecture Decision Records - consult and manage ADRs for project design decisions | **Skill:** `adr` - Auto-invoked when planning features, modifying core systems, or making architectural decisions. Includes reference guide on ADR best practices. |
| [skill-reviewer](./skill-reviewer/) | Review SKILL.md files for quality, completeness, and best practices | **Skill:** `skill-reviewer` - Review a SKILL.md against the authoring checklist. **Agent:** `skill-reviewer` - Subagent for programmatic skill audits. |

## Installation

Add this marketplace to your Claude Code settings, then install plugins:

```bash
claude plugin install teja-skills/adr
claude plugin install teja-skills/skill-reviewer
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
