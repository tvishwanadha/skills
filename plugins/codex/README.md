# Codex Plugin

Codex-powered code review, plan review, and completion verification.

## Overview

The Codex plugin drives the OpenAI Codex CLI (`codex exec`) for automated review workflows. It teaches an agent to use Codex for reviewing plans before implementation, verifying completion after context compactions, and reviewing code after significant changes.

## Prerequisites

- [Codex CLI](https://github.com/openai/codex) installed and on PATH
- `jq` installed and on PATH

## Skills

### codex (guide)

Background reference for Codex CLI recipes, thread lifecycle, sandbox selection, and failure handling. Auto-loaded when an agent or skill needs to run Codex.

### review

**Trigger phrases:** "codex review", "review with codex", "double-check this", "verify this is finished"

A critical, read-only second opinion from Codex on a plan, an implementation, or a code change. Start a new review or continue an existing thread after changes, then vet the findings - pushing back until the list is one you would stand behind. Use before exiting plan mode, before declaring work done (especially after a context compaction), or after writing significant code.

## Agents

### review

A read-only Codex review agent, shared by the review skill and other Codex review types.

## CLI Recipes

The plugin bundles no server or binary. The `codex` guide skill documents two `codex exec --json | jq` recipes - start a thread and resume it - that the skills and agent in this plugin build on.

## Installation

Claude Code only - this plugin drives the Codex CLI from a Claude session, so running it inside Codex would just recurse.

```bash
claude plugin install teja-skills/codex
```

## See also

OpenAI has released an official [Codex plugin for Claude Code](https://github.com/openai/codex-plugin-cc) ([announcement](https://community.openai.com/t/introducing-codex-plugin-for-claude-code/1378186)). It drives the local Codex CLI/app server directly and ships ready-made task commands such as `/codex:adversarial-review`.

This plugin takes a different angle: it exposes a Codex prompt as a composable primitive (`codex exec` recipes) that skills and agents build on - plan review, completion verification, and code review woven into agent workflows. Reach for theirs when you want ready-made review and delegation commands; reach for this one when you want Codex review embedded in skill and agent workflows.

## License

MIT
