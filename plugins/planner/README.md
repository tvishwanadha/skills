# Planner

Enhanced planning for Claude Code's built-in plan mode. Adds complexity scoring, sub-problem decomposition for complex tasks, and plan review.

## Skills

| Skill | Type | Description |
|-------|------|-------------|
| `planning-methodology` | Guide | Auto-invokes during planning to score complexity, guide decomposition, and trigger plan review |
| `review-plan` | Review | Reviews plans for completeness, feasibility, risk coverage, architectural alignment, and coherence |

## How It Works

The `planning-methodology` guide skill augments the built-in plan mode phases:

1. **Phase 1 - Understanding**: After codebase exploration, scores the task across 7 complexity dimensions (scope, novelty, dependencies, ambiguity, risk, concurrency, domain)
2. **Phase 2 - Design**: For complex tasks (composite 7+, or any dimension >= 8), decomposes into sub-problems with parallel Plan agents. Moderate tasks get thorough research. Simple tasks proceed directly.
3. **Phase 3 - Review**: Before ExitPlanMode, invokes `review-plan` to validate the plan against the codebase

## Setup

Add to your project's `CLAUDE.md`:

```
When in plan mode, invoke planner:planning-methodology for enhanced planning.
```

## Usage

Planning methodology loads automatically during plan mode. Plan review can also be invoked directly:

```
/planner:review-plan .claude/plans/my-plan.md
/planner:review-plan
```

## Optional Integrations

- **reviewer plugin**: If installed, `review-plan` uses the `reviewer-framework` severity/confidence format. Without it, a built-in format is used.
- **self-review-extension**: `review-plan` can be wired into the self-review orchestrator as a custom review type for plan files.

## Installation

```bash
claude plugin install teja-skills/planner
```

Or for local testing:

```bash
claude --plugin-dir ./plugins/planner
```
