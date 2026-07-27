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

Add this entry to your project marketplace at `<your-repo>/.agents/plugins/marketplace.json`:

```json
{
  "name": "adr",
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/tvishwanadha/skills.git",
    "path": "./plugins/adr",
    "ref": "main"
  },
  "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
  "category": "Developer Tools"
}
```

Then `codex plugin marketplace add .` and `codex plugin add adr@project-plugins`. See the [marketplace setup recipe](../../README.md#codex) for the full file and its caveats.

## License

MIT
