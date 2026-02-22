# Planner

Enhanced planning for Claude Code's built-in plan mode. Adds complexity scoring, sub-problem decomposition for complex tasks, and plan review.

## Skills

| Skill | Type | Description |
|-------|------|-------------|
| `planning-methodology` | Guide | Auto-invokes during planning to score complexity, guide decomposition, and trigger plan review |
| `review-plan` | Review | Reviews plans for completeness, feasibility, risk coverage, architectural alignment, and coherence |
| `customize-plan-review` | Interactive | Creates or modifies a `local-plan-standards` skill for project-specific planning constraints |

## How It Works

The `planning-methodology` guide skill is invoked during plan mode. It drafts a lightweight plan, scores complexity against the draft, then fleshes out the design - decomposing complex tasks into sub-problems. Every plan is reviewed before presenting to the user.

## Setup

Add to your project's `CLAUDE.md`:

```
ALWAYS invoke planner:planning-methodology when entering plan mode.
```

## Usage

Planning methodology loads automatically during plan mode. Plan review can also be invoked directly:

```
/planner:review-plan .claude/plans/my-plan.md
/planner:review-plan
```

To create project-specific planning constraints:

```
/planner:customize-plan-review
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
