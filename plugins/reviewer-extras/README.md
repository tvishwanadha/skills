# Reviewer Extras Plugin

Extra review types for the reviewer framework that depend on other plugins.

## Overview

This plugin provides composite review types that bridge multiple plugins together. Each skill requires specific dependencies to be installed alongside this plugin.

## Skills

| Skill | Description | Required plugins |
|-------|-------------|------------------|
| `review-codex` | Independent second-opinion review run inside a Codex thread | `teja-skills/reviewer`, `teja-skills/codex` |
| `review-claude-md` | CLAUDE.md and project context file quality and structure | `teja-skills/reviewer`, `claude-plugins-official/claude-md-management` |

## Installation

### Claude Code

```bash
claude plugin install teja-skills/reviewer-extras
```

### Codex

Add this entry to your project marketplace at `<your-repo>/.agents/plugins/marketplace.json`:

```json
{
  "name": "reviewer-extras",
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/tvishwanadha/skills.git",
    "path": "./plugins/reviewer-extras",
    "ref": "main"
  },
  "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
  "category": "Developer Tools"
}
```

Then `codex plugin marketplace add .` and `codex plugin add reviewer-extras@project-plugins`. See the [marketplace setup recipe](../../README.md#codex) for the full file and its caveats.

The dependency plugins listed above need their own marketplace entries, same shape. Claude plugins install on Codex even without a Codex manifest.

## License

MIT
