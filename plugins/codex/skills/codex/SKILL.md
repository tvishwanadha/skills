---
name: codex
description: >-
  This skill should be loaded when an agent or skill needs to run the Codex
  CLI for code review, plan review, or completion verification. Covers the
  `codex exec --json | jq` recipes for starting and resuming a thread, reading
  the event stream, detecting outcomes, and choosing a sandbox.
user-invocable: false
---

# Codex CLI Guide

## Start a thread

```bash
codex exec --json --sandbox read-only - <<'EOF' | jq -c 'select(.type=="thread.started" or .type=="turn.failed" or (.type=="item.completed" and .item.type=="agent_message"))'
<your prompt>
EOF
```

## Continue a thread

```bash
codex exec --json --sandbox read-only resume <threadId> - <<'EOF' | jq -c 'select(.type=="thread.started" or .type=="turn.failed" or (.type=="item.completed" and .item.type=="agent_message"))'
<your follow-up>
EOF
```

Pass `--sandbox` on every call, including `resume`, and place it before the subcommand - it applies per invocation, not per thread. Do not set the model.

## Read the output

The first line is `{"type":"thread.started","thread_id":"<uuid>"}` - save `thread_id` for resuming. A turn commonly emits several `agent_message` items; the answer is the LAST `item.completed` line with `item.type=="agent_message"`, at `.item.text`.

## Detect the outcome

Do not trust the pipeline's exit status - it is jq's, not Codex's.

- A `turn.failed` line means the turn failed - it is the authoritative failure signal. The thread id from the first line is still valid - resume it.
- `thread.started` but no `agent_message` line and no `turn.failed` line means the turn produced no answer (killed, timed out, or truncated) - treat it as a failure and resume the thread.
- No `thread.started` line at all means the thread never started - read stderr for the cause.
- Ignore the stderr line `Reading additional input from stdin...` - it is not a failure.

## Choose a sandbox

- `read-only` for review - Codex can read outside the working root but cannot write anywhere or reach the network.
- `workspace-write` when Codex must edit files or run tests.

## Working root

The working root is the directory you invoke `codex` from. When the session is in a git worktree, pass `-C <absolute worktree path>` on every call - start and resume - before the subcommand, so Codex works in the worktree rather than the main checkout:

```bash
codex exec -C /abs/path/to/worktree --json --sandbox read-only - <<'EOF' | jq -c '...'
```

Use a literal path, not `$(...)` - command substitution defeats the `Bash(codex exec:*)` permission grant. Omit `-C` when the session is not in a worktree. Inline anything not on disk into the prompt - a plan from chat, a discussion, prior decisions.

## Long calls

A foreground shell call times out at 2 minutes by default, 10 minutes at most with an explicit timeout. Run Codex calls in the background and read the output when the call completes.

## Thread id lifecycle

Record the thread id in the session's working plan or task list so it survives context compaction. If neither exists, create a task entry and record it there.

If the thread id is lost, start a fresh thread with context recovery info.
