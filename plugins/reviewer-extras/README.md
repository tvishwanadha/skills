# Reviewer Extras Plugin

Extra review types for the reviewer framework that depend on other plugins.

## Overview

This plugin provides composite review types that bridge multiple plugins together. Each skill requires specific dependencies to be installed alongside this plugin.

## Dependencies

| Plugin | Required by | Purpose |
|--------|-------------|---------|
| `reviewer` | `review-codex` | Output format, severity levels, confidence scoring via `reviewer-framework` |
| `codex` | `review-codex` | Codex MCP tools and review workflow via `codex:review` |

## Skills

| Skill | Description | Extra Dependencies |
|-------|-------------|--------------------|
| `review-codex` | Deep code review using Codex MCP tools with reviewer framework output | `reviewer`, `codex` |

## Installation

Install this plugin and its dependencies:

```bash
claude plugin install teja-skills/reviewer
claude plugin install teja-skills/codex
claude plugin install teja-skills/reviewer-extras
```

## License

MIT
