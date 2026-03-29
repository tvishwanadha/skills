# ADR Plugin

Architecture Decision Records - consult and manage ADRs for project design decisions.

## Overview

The ADR plugin teaches an agent to work with Architecture Decision Records as a project's design knowledge base. It ensures ADRs are consulted before planning features or making architectural changes, and guides the creation of new ADRs when significant decisions are made.

## Skills

### adr

**Trigger phrases:** "check ADRs", "architecture decision", "why was this designed", "planning implementation", "create an ADR"

**What it covers:**
- Consulting existing ADRs before planning
- ADR status lifecycle (Proposed, Accepted, Superseded, Deprecated)
- Reading priority based on status
- Creating new ADRs for architecturally significant decisions
- Writing quality guidance and anti-patterns

## Installation

### Claude Code

```bash
claude plugin install teja-skills/adr
```

### Codex

Official plugin publishing is [coming soon](https://developers.openai.com/codex/plugins/build#publish-official-public-plugins). In the meantime, add this plugin to your repo or personal marketplace at `~/.agents/plugins/marketplace.json`.

## License

MIT
