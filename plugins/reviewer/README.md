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

### Creation Skills

| Skill | Description | Usage |
|-------|-------------|-------|
| `reviewer-init` | Interactive setup wizard | `/reviewer:reviewer-init` |
| `customize-core-review` | Add project-specific rules to a built-in review type | `/reviewer:customize-core-review patterns` |
| `add-core-review` | Create a new custom review type | `/reviewer:add-core-review security` |
| `extend-self-review` | Customize orchestrator behavior | `/reviewer:extend-self-review` |
| `create-reviewer-agent` | Create a custom reviewer agent | `/reviewer:create-reviewer-agent fast-reviewer` |

### Framework

| Skill | Description |
|-------|-------------|
| `reviewer-framework` | Shared review methodology (guide skill, not user-invocable) |

## Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| `reviewer` | Opus | Deep reasoning code reviewer for logic analysis and skill quality |
| `simple-reviewer` | Sonnet | Fast code reviewer for pattern matching and documentation checks |

## How It Works

### Self-Review Flow

```
/reviewer:self-review --diff main
  |
  +-- [check for self-review-extension skill]
  |
  +-- Launch parallel Tasks:
  |     +-- reviewer agent -> review-logic -> [local-review-logic?] -> default-logic.md
  |     +-- simple-reviewer agent -> review-patterns -> [local-review-patterns?] -> default-patterns.md
  |     +-- simple-reviewer agent -> review-documentation -> [local-review-documentation?] -> default-documentation.md
  |     +-- reviewer agent -> review-skill -> [local-review-skill?] -> default-skill.md (if SKILL.md in scope)
  |
  +-- Coalesce: deduplicate, filter by confidence threshold (default >= 80), sort by severity
  |
  +-- Present unified report
```

### Core Skill Loading

Each core review skill uses progressive loading:

1. Try to invoke the local skill `local-review-x` via the Skill tool
2. Based on result:
   - **Local found + prohibits defaults**: use only local guidance
   - **Local found + allows defaults**: combine local + defaults
   - **Local not found**: use plugin defaults only
3. Read defaults from `references/default-x.md` (only when needed)

## Local Customization

Projects can customize reviews without forking the plugin. Use the creation skills for guided setup, or create local skills manually.

### Quick Setup

Run the interactive wizard to configure everything at once:

```
/reviewer:reviewer-init
```

### Extending a Core Review

Add project-specific rules on top of the defaults:

```
/reviewer:customize-core-review patterns
```

This creates `.claude/skills/local-review-patterns/SKILL.md` with project-specific rules that combine with the plugin defaults.

### Overriding a Core Review

Replace defaults entirely with a custom ruleset. Use `/reviewer:customize-core-review <type>` and select "override" mode, or create the skill manually:

```markdown
---
name: local-review-logic
description: Custom logic review rules for my-project.
user-invocable: false
---

# Local Review: Logic

Do not use the default review rules. Apply only these checks:

- All async functions must have timeout handling
- Database transactions must use the `with_transaction` wrapper
- Error types must implement the project's `AppError` trait
```

### Adding Custom Review Types

Create a new review category (e.g., security, performance):

```
/reviewer:add-core-review security
```

This creates `.claude/skills/review-security/SKILL.md` and optionally wires it into the self-review orchestrator.

### Extending the Orchestrator

Modify which review types run, change agents, or adjust thresholds:

```
/reviewer:extend-self-review
```

This creates `.claude/skills/self-review-extension/SKILL.md` to customize the self-review configuration.

### What Belongs Where

| Rules | Location | Example |
|-------|----------|---------|
| Language-agnostic defaults | Plugin `references/default-x.md` | "consistent naming within module" |
| Project-specific additions | Local `local-review-x` skill | "k8s resources use `<app>-<component>`" |
| Complete custom ruleset | Local `local-review-x` skill (with "do not use defaults") | Full project-specific rules |
| Custom review categories | Local `review-x` skill | Security, performance, domain-specific |
| Orchestrator customization | Local `self-review-extension` skill | Add/remove review types, adjust threshold |

## Installation

```bash
claude plugin install teja-skills/reviewer
```

## License

MIT
