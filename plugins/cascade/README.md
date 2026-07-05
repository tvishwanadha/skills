# cascade

Multi-model orchestration hierarchy - the session model orchestrates and signs off, Opus leads vertical slices, Sonnet implements, Haiku runs mechanical tasks.

## How it works

Work flows down the hierarchy; plans, evidence, and escalations flow back up. Dashed edges are direct delegation: the main session hands work that does not need a full cascade straight to the lower tiers (the Setup section makes this the default).

```mermaid
graph TD
    user([user]) -- "goal or plan" --> session[main session]
    session -- "slice brief" --> lead["slice-lead (Opus)"]
    lead -- "plan, then report" --> session
    lead -- "approved plan" --> impl["implementer (Sonnet)"]
    impl -- "status report" --> lead
    impl -- "commands" --> mech["mechanic (Haiku)"]
    mech -- "results, failures in full" --> impl
    lead -- "commands" --> mech
    session -. "small task" .-> impl
    session -. "commands" .-> mech
```

`/cascade:orchestrate <goal>` runs in the main session, which owns the goal end to end. It takes a goal to break down, or a plan that already converged (in conversation or plan mode) - each lead then validates its pre-approved portion instead of authoring a plan:

1. Restate the goal as acceptance criteria, slice it vertically, and size each slice against a complexity rubric - oversized slices are re-cut before any delegation.
2. Delegate each ready slice to its own `slice-lead`, which returns a plan and pauses - or NEEDS_RESLICING if the slice turns out bigger than its brief.
3. Review the plan, iterate via feedback to the same lead, approve explicitly.
4. The lead delegates implementation to the `implementer`, which routes builds, lints, tests, and shell commands through the `mechanic`.
5. The lead reviews the slice diff, routes findings back for fixes, then reports; the orchestrator validates independently and signs off.

A slice starts as soon as the orchestrator has signed off the slices it depends on - a lead reporting done is not sign-off - and runs concurrently with the rest; slices that touch the same files take turns.

Ambiguity travels up the chain; no tier resolves unclear instructions by guessing.

## Setup

To make delegation the default rather than opt-in, add to your project's `CLAUDE.md`:

```
Delegate implementation to the cascade `implementer` agent and command execution to the cascade `mechanic` agent - the main session plans, reviews, and decides; it does not write code or run builds and tests itself. Reserve direct shell use for trivial read-only one-liners.

When a plan converges - in plan mode or in conversation - hand execution to `cascade:orchestrate`.
```

The first line makes the cheap tiers the default executors for all work; the second routes every approved plan into the cascade. Omit either to keep that behavior opt-in.

## Components

| Component | Model | Role |
|-----------|-------|------|
| `orchestrate` (skill) | session model | Slices the goal, reviews plans, validates, signs off |
| `slice-lead` (agent) | opus | Plans a slice, delegates implementation, reviews, reports |
| `implementer` (agent) | sonnet | Executes the approved plan |
| `mechanic` (agent) | haiku | Runs commands exactly as asked |

## Prerequisites

- Claude Code with subagent nesting (see the diagram for who spawns whom).

## Installation

```bash
claude plugin install teja-skills/cascade
```

This plugin is Claude-specific - it pins agents to Claude model tiers and relies on Claude Code subagent nesting - so there is no Codex variant.

## License

MIT
