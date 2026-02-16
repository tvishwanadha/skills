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

**Resources:**
- Core SKILL.md with quick reference and workflows
- `references/creating-adrs.md` - step-by-step creation procedure
- `references/writing-adrs.md` - content quality and anti-patterns
- `references/adr-guide.md` - background on ADRs and templates
- `assets/nygard-template.md` - standard ADR template
- `assets/init.md` - ADR directory initialization

## Installation

```
/plugin marketplace add tvishwanadha/teja-skills
/plugin install adr
```

## License

MIT
