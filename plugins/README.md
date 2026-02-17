# Teja's Plugin Marketplace

Personal collection of Claude Code plugins for reuse across projects.

## Plugins

| Name | Description | Contents |
|------|-------------|----------|
| [adr](./adr/) | Architecture Decision Records - consult and manage ADRs for project design decisions | **Skill:** `adr` - Auto-invoked when planning features, modifying core systems, or making architectural decisions. Includes reference guide on ADR best practices. |
| [reviewer](./reviewer/) | Layered code review framework with extensible core reviews, parallel orchestration, and project-local customization | **Skills:** `self-review` (orchestrator), `plan-review` (orchestrator), `review-skill`, `review-logic`, `review-patterns`, `review-documentation`, `reviewer-framework` (guide). **Agent:** `reviewer` (opus). |
| [codex](./codex/) | Codex-powered code review, plan review, and completion verification | **MCP:** `codex` (Codex mcp-server). **Skills:** `codex` (guide - MCP tool usage), `review` (review workflows). **Agent:** `review` - Preloads both skills. |

## Installation

Add this marketplace to your Claude Code settings, then install plugins:

```bash
claude plugin install teja-skills/adr
claude plugin install teja-skills/reviewer
claude plugin install teja-skills/codex
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
