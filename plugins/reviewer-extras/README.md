# Reviewer Extras Plugin

Extra review types for the reviewer framework that depend on other plugins.

## Overview

This plugin provides composite review types that bridge multiple plugins together. Each skill requires specific dependencies to be installed alongside this plugin.

## Skills

| Skill | Description | Required plugins |
|-------|-------------|------------------|
| `review-codex` | Deep code review using Codex MCP tools | `teja-skills/reviewer`, `teja-skills/codex` |
| `review-claude-md` | CLAUDE.md and project context file quality and structure | `teja-skills/reviewer`, `claude-plugins-official/claude-md-management` |

## Installation

### Claude Code

```bash
claude plugin install teja-skills/reviewer-extras
```

### Codex

Official plugin publishing is [coming soon](https://developers.openai.com/codex/plugins/build#publish-official-public-plugins). In the meantime, add this plugin to your repo or personal marketplace at `~/.agents/plugins/marketplace.json`.

## License

MIT
