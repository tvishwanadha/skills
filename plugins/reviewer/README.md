# Reviewer Plugin

Layered code review framework with extensible core reviews, parallel orchestration, and project-local customization.

## Overview

The reviewer plugin provides a structured review system with three layers:

1. **Core review skills** (named `review-*`) - individual review types with default rules and local override support
2. **Orchestrator skills** (named `*-review`) - top-level entry points that launch reviews in parallel
3. **Shared framework** (`reviewer-framework`) - common methodology, output format, and scoring

## Skills

### Orchestrator

| Skill | Description | Usage |
|-------|-------------|-------|
| `self-review` | Parallel code review across multiple review types | `/reviewer:self-review --diff main` |

### Core Review Types

| Skill | Focus |
|-------|-------|
| `review-skill` | SKILL.md quality, frontmatter, structure, conventions |
| `review-logic` | Control flow, edge cases, error handling, state management |
| `review-patterns` | Naming, structure, style consistency, test quality |
| `review-documentation` | README accuracy, API docs, completeness |

## Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| `reviewer` | Opus | Deep reasoning code reviewer for logic analysis and skill quality |
| `simple-reviewer` | Sonnet | Fast code reviewer for pattern matching and documentation checks |

## Usage

| Goal | Command |
|------|---------|
| Review changes | `/reviewer:self-review --diff main` |
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

Official plugin publishing is [coming soon](https://developers.openai.com/codex/plugins/build#publish-official-public-plugins). In the meantime, add this plugin to your repo or personal marketplace at `~/.agents/plugins/marketplace.json`.

Codex plugins cannot contribute subagents - they must be added manually. Create TOML files in `.codex/agents/` at the project root:

`.codex/agents/reviewer.toml`:

```toml
name = "reviewer"
description = "Deep code reviewer for logic analysis, skill quality audits, and complex review types. Invoke via Task tool with a review skill and scope."
model = "gpt-5.4"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
You are a code reviewer. Your task prompt will specify which review skill to invoke and what scope to review. Follow the reviewer-framework conventions for output format and confidence scoring.

Invoke the specified review skill using the Skill tool, passing the scope as its argument. The review skill will load its rules (checking for local overrides first, then defaults) and guide you through the review procedure.

Return all findings with confidence scores. Do not filter by threshold - the orchestrator handles filtering.
"""
```

`.codex/agents/simple-reviewer.toml`:

```toml
name = "simple-reviewer"
description = "Fast code reviewer for pattern matching, documentation checks, and straightforward review types. Invoke via Task tool with a review skill and scope."
model = "gpt-5.4-mini"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
You are a code reviewer. Your task prompt will specify which review skill to invoke and what scope to review. Follow the reviewer-framework conventions for output format and confidence scoring.

Invoke the specified review skill using the Skill tool, passing the scope as its argument. The review skill will load its rules (checking for local overrides first, then defaults) and guide you through the review procedure.

Return all findings with confidence scores. Do not filter by threshold - the orchestrator handles filtering.
"""
```

Adjust `model` to your preferred model. The `reviewer-framework` skill is automatically available from the installed plugin. See the [Codex subagents guide](https://developers.openai.com/codex/subagents) for the full TOML schema, including `[[skills.config]]` for custom skill configuration.

## License

MIT
