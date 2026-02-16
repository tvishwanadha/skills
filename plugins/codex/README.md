# Codex Plugin

Codex-powered code review, plan review, and completion verification.

## Overview

The Codex plugin integrates OpenAI Codex as an MCP server for automated review workflows. It teaches an agent to use Codex for reviewing plans before implementation, verifying completion after context compactions, and reviewing code after significant changes.

## Prerequisites

- [Codex CLI](https://github.com/openai/codex) installed and on PATH

## Skills

### codex (guide)

Background reference for Codex MCP tool usage, thread configuration, and thread lifecycle. Auto-loaded when an agent or skill needs Codex MCP tool reference.

### review

**Trigger phrases:** "review plan", "verify completion", "code review", "codex review"

**Workflows:**
- **Plan review** - review before exiting plan mode
- **Completion verification** - verify after implementation or context compaction
- **Code review** - review after writing significant changes

All workflows iterate on Codex feedback until approved.

## Agents

### review

A read-only review agent that follows the review skill workflows using Codex MCP tools. Provides feedback only - does not write or edit files.

## MCP Server

The plugin bundles an MCP server configuration that runs `codex mcp-server`. When installed, Claude Code provides two tools:
- `codex` - start a review thread
- `codex-reply` - continue an existing thread

## Installation

```
/plugin marketplace add tvishwanadha/teja-skills
/plugin install codex
```

## License

MIT
