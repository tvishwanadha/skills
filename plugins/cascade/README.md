# cascade

Multi-model orchestration hierarchy - the session model orchestrates and signs off, Opus leads vertical slices, Sonnet implements, Haiku runs mechanical tasks.

## How it works

`/cascade:orchestrate <goal>` runs in the main session, which owns the goal end to end:

1. Restate the goal as acceptance criteria and slice it vertically.
2. Delegate each slice to the `slice-lead` agent (Opus), which returns a plan and pauses.
3. Review the plan, iterate via feedback to the same lead, approve explicitly.
4. The lead delegates implementation to `implementer` (Sonnet), which federates builds, lints, tests, and shell commands to `mechanic` (Haiku) - successes come back summarized, failures verbatim and in full.
5. The lead self-reviews the slice diff, routes findings back for fixes, then reports; the orchestrator validates independently and signs off.

Ambiguity travels up the chain (mechanic -> implementer -> lead -> orchestrator -> user); no tier resolves unclear instructions by guessing.

## Components

| Component | Model | Role |
|-----------|-------|------|
| `orchestrate` (skill) | session model | Slices the goal, reviews plans, validates, signs off |
| `slice-lead` (agent) | opus | Plans a slice, delegates implementation, reviews, reports |
| `implementer` (agent) | sonnet | Executes the approved plan |
| `mechanic` (agent) | haiku | Runs commands exactly as asked |

## Prerequisites

- Claude Code >= 2.1.172, for subagent nesting (the lead spawns the implementer, which spawns the mechanic).
- The [reviewer](../reviewer/) plugin, for the lead's self-review step. Without it the lead falls back to reviewing the slice diff itself and declares that in its report.
- Slices run one at a time: leads must run as foreground subagents because backgrounded subagents cannot load skills.

## Installation

```bash
claude plugin install teja-skills/cascade
```

This plugin is Claude-specific - it pins agents to Claude model tiers and relies on Claude Code subagent nesting - so there is no Codex variant.
