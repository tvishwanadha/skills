# Reviewer Plugin

Layered code review framework with extensible core reviews, parallel orchestration, and project-local customization.

## Overview

The reviewer plugin provides a structured review system with three layers:

1. **Core review skills** (named `review-*`) - individual review types with default rules and local override support
2. **Orchestrator skills** (named `*-review`) - top-level entry points that launch reviews in parallel
3. **Shared framework** (`reviewer-framework`) - common methodology, output format, and scoring

## Skills

### Orchestrators

| Skill | Description | Usage |
|-------|-------------|-------|
| `self-review` | Parallel code review across multiple review types | `/reviewer:self-review --diff main` |
| `plan-review` | Review implementation plans for completeness and feasibility | `/reviewer:plan-review path/to/plan.md` |

### Core Review Types

| Skill | Focus |
|-------|-------|
| `review-skill` | SKILL.md quality, frontmatter, structure, conventions |
| `review-logic` | Control flow, edge cases, error handling, state management |
| `review-patterns` | Naming, structure, style consistency, test quality |
| `review-documentation` | README accuracy, API docs, completeness |

### Framework

| Skill | Description |
|-------|-------------|
| `reviewer-framework` | Shared review methodology (guide skill, not user-invocable) |

## Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| `reviewer` | Opus | Deep reasoning code reviewer, used by orchestrators |

## How It Works

### Self-Review Flow

```
/reviewer:self-review --diff main
  |
  +-- [check for self-review-extension skill]
  |
  +-- Launch parallel Tasks:
  |     +-- reviewer agent -> review-logic -> [local-review-logic?] -> default-logic.md
  |     +-- reviewer agent -> review-patterns -> [local-review-patterns?] -> default-patterns.md
  |     +-- reviewer agent -> review-documentation -> [local-review-documentation?] -> default-documentation.md
  |     +-- reviewer agent -> review-skill -> [local-review-skill?] -> default-skill.md (if SKILL.md in scope)
  |
  +-- Coalesce: deduplicate, filter by confidence >= 80, sort by severity
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

Projects can customize reviews without forking the plugin by creating local skills.

### Extending a Core Review

Add project-specific rules on top of the defaults. Create a local skill:

```
.claude/skills/local-review-patterns/SKILL.md
```

```markdown
---
name: local-review-patterns
description: Project-specific pattern review rules for my-project.
user-invocable: false
---

# Local Review Patterns

Apply these project-specific patterns in addition to the defaults:

- Kubernetes resources use `<app>-<component>-<resource>` naming
- All public types must derive Debug and Clone
- API handlers follow the `handle_<verb>_<resource>` convention
- Database queries use the repository pattern, never inline SQL
```

### Overriding a Core Review

Replace defaults entirely with a custom ruleset:

```markdown
---
name: local-review-logic
description: Custom logic review rules for my-project.
user-invocable: false
---

# Local Review Logic

Do not use the default review rules. Apply only these checks:

- All async functions must have timeout handling
- Database transactions must use the `with_transaction` wrapper
- Error types must implement the project's `AppError` trait
```

### Extending an Orchestrator

Modify which review types run, change agents, or adjust thresholds:

```
.claude/skills/self-review-extension/SKILL.md
```

```markdown
---
name: self-review-extension
description: Customize self-review for my-project.
user-invocable: false
---

# Self-Review Extension

Modify the default self-review configuration:

- Add `review-security` to the review types list (using `reviewer:reviewer` agent)
- Remove `review-documentation` from the list (we handle docs separately)
- Set confidence threshold to 70 (we want more findings)
- Use `reviewer:simple-reviewer` for `review-patterns` (faster, patterns are straightforward)
```

### What Belongs Where

| Rules | Location | Example |
|-------|----------|---------|
| Language-agnostic defaults | Plugin `references/default-x.md` | "consistent naming within module" |
| Project-specific additions | Local `local-review-x` skill | "k8s resources use `<app>-<component>`" |
| Complete custom ruleset | Local `local-review-x` skill (with "do not use defaults") | Full project-specific rules |
| Orchestrator customization | Local `self-review-extension` or `plan-review-extension` | Add/remove review types, adjust threshold |

## Installation

```bash
claude plugin install teja-skills/reviewer
```

## License

MIT
