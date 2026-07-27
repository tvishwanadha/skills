# Reviewer Plugin

Layered code review framework with extensible core reviews, parallel orchestration, and project-local customization.

## Overview

The reviewer plugin provides a structured review system with three layers:

1. **Core review skills** (named `review-*`) - individual review types with default rules and local override support
2. **Orchestrator skills** (named `*-review`) - top-level entry points that launch reviews in parallel
3. **Shared framework** (`reviewer-framework`) - common methodology, output format, and scoring

The orchestrator groups review types by (agent, scope) and spawns one reviewer subagent per group to run its assigned review skills over a shared context.

## Skills

### Orchestrator

| Skill | Description | Usage |
|-------|-------------|-------|
| `self-review` | Parallel code review across multiple review types, then hands off to fixing | `reviewer:self-review --diff main` |

### Fixing

| Skill | Description | Usage |
|-------|-------------|-------|
| `fix-findings` | Cluster verified findings and apply fixes interactively; invoked automatically after `self-review` | `reviewer:fix-findings` |

### Core Review Types

| Skill | Focus |
|-------|-------|
| `review-skill` | SKILL.md quality, conventions, and cross-skill correctness |
| `review-logic` | Control flow, edge cases, error handling, state management, data integrity, injection |
| `review-patterns` | Conventions, structure, tests, and reuse/efficiency cleanups |
| `review-documentation` | README accuracy, API docs, completeness, staleness, link integrity |

## Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| `reviewer` | Opus | Runs one or more review skills over a shared context; deep reasoning for logic analysis and skill quality |
| `simple-reviewer` | Sonnet | Runs one or more review skills over a shared context; fast pattern matching and documentation checks |

## Usage

Commands below use Claude Code's `/` prefix; on Codex, invoke with `$` instead.

| Goal | Command |
|------|---------|
| Review changes | `/reviewer:self-review --diff main` |
| Apply fixes for findings | `/reviewer:fix-findings` |
| Set up for your project | `/reviewer:reviewer-init` |
| Add project-specific rules | `/reviewer:customize-core-review <type>` |
| Add a new review category | `/reviewer:add-core-review <name>` |
| Customize the orchestrator | `/reviewer:extend-self-review` |
| Create a custom agent | `/reviewer:create-reviewer-agent <name>` |

## Installation

### Claude Code

```bash
claude plugin install teja-skills/reviewer
```

### Codex

Add this entry to your project marketplace at `<your-repo>/.agents/plugins/marketplace.json`:

```json
{
  "name": "reviewer",
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/tvishwanadha/skills.git",
    "path": "./plugins/reviewer",
    "ref": "main"
  },
  "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
  "category": "Developer Tools"
}
```

Then `codex plugin marketplace add .` and `codex plugin add reviewer@project-plugins`. See the [marketplace setup recipe](../../README.md#codex) for the full file and its caveats.

Codex plugins cannot contribute subagents - they must be added manually. Copy this repo's agent definitions - [`.codex/agents/reviewer.toml`](../../.codex/agents/reviewer.toml) and [`.codex/agents/simple-reviewer.toml`](../../.codex/agents/simple-reviewer.toml) - into `.codex/agents/` at your project root (or `~/.codex/agents/` for personal use).

Set each agent's `model` to your preference - a capable model for `reviewer`, a fast/lower-cost one for `simple-reviewer`. The `reviewer-framework` skill is automatically available from the installed plugin. See the [Codex subagents guide](https://learn.chatgpt.com/docs/agent-configuration/subagents.md) for the full TOML schema, including `[[skills.config]]` for custom skill configuration.

The `create-reviewer-agent` skill is Claude Code-only - it writes a Markdown agent definition. On Codex, author custom reviewer agents by hand as TOML, following the same pattern as the built-in agents above.

## License

MIT
