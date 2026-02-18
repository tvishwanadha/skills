# Reviewer Extras Plugin

Extra review types for the reviewer framework that depend on other plugins.

## Overview

This plugin provides composite review types that bridge multiple plugins together. Each skill requires specific dependencies to be installed alongside this plugin.

## Dependencies

| Plugin | Marketplace | Required by | Purpose |
|--------|-------------|-------------|---------|
| `reviewer` | `teja-skills` | `review-codex`, `review-claude-md` | Output format, severity levels, confidence scoring via `reviewer-framework` |
| `codex` | `teja-skills` | `review-codex` | Codex MCP tools and review workflow via `codex:review` |
| `claude-md-management` | `claude-plugins-official` | `review-claude-md` | CLAUDE.md quality rubric via `claude-md-improver` |

## Skills

| Skill | Description | Extra Dependencies |
|-------|-------------|--------------------|
| `review-codex` | Deep code review using Codex MCP tools with reviewer framework output | `reviewer`, `codex` |
| `review-claude-md` | CLAUDE.md and project context file quality, structure, and inter-file relationships | `reviewer`, `claude-md-management` |

## License

MIT
