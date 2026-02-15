---
name: adr
description: Architecture Decision Records - the project's design knowledge base. CONSULT ADRs before planning any feature or architectural change. Use when (1) planning implementation, (2) modifying core systems, (3) asked why something works a certain way, (4) making significant technical decisions. ADRs explain WHY choices were made.
---

# Architecture Decision Records

ADRs are the project's architectural knowledge base. **Consult them before planning.**

## Quick Reference

| Status | Priority | Meaning |
|--------|----------|---------|
| **Accepted** | 1st | Active decision, governs implementation |
| **Superseded** | 2nd | Read superseding ADR, original has context |
| **Amended** | 2nd | Read with original together |
| **Proposed** | 3rd | Under discussion, not yet binding |
| **Deprecated** | 4th | Historical only, no longer applies |

| Command | Purpose |
|---------|---------|
| `npx adrjs new "Title"` | Create new ADR |
| `npx adrjs new -s N "Title"` | Supersede ADR N |
| `npx adrjs new -a N "Title"` | Amend ADR N |

---

## Current ADRs

!`ls docs/adr/*.md 2>/dev/null | xargs -I {} basename {} | sort || echo "No ADRs found"`

## Reading Priority

When consulting ADRs, follow this priority:

1. **Accepted** - Active decisions that govern current implementation
2. **Superseded chain** - Read the superseding ADR, but check original for context
3. **Amended** - Read both the amendment AND the original together
4. **Proposed** - Under discussion, not yet binding
5. **Deprecated** - Historical context only, no longer applies

**Key principle**: If an ADR is superseded, the superseding ADR takes precedence, but the original often explains the journey.

---

## Before Planning (IMPORTANT)

**Always consult ADRs before implementing features or making changes.**

1. List ADRs to find relevant decisions:
   ```bash
   ls docs/adr/
   ```

2. Read ADRs related to the subsystem you're modifying

3. Follow accepted decisions unless explicitly asked to change them

4. If your plan contradicts an ADR, discuss with the user before proceeding

### Finding Relevant ADRs

| Working on... | Check ADRs about... |
|---------------|---------------------|
| Runtime/isolation | modes, gvisor, standard |
| Suspend/resume | suspend, resume, state |
| Kubernetes resources | deployment, topology |
| Testing | test, integration |
| Storage/S3 | overlay, storage |

---

## Creating ADRs

Use `npx adrjs` for ADR operations:

| Command | Purpose |
|---------|---------|
| `npx adrjs new "Title"` | Create new ADR |
| `npx adrjs new -s N "Title"` | Create ADR that supersedes ADR N |
| `npx adrjs new -a N "Title"` | Create ADR that amends ADR N |

### When to Create an ADR

Create an ADR when the decision:
- Affects system structure or key quality attributes
- Would be costly or difficult to reverse
- Involves tradeoffs between competing concerns
- Will guide future implementation choices

### Template Structure

```markdown
# N. Title

Date: YYYY-MM-DD

## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-N]

## Context
[The issue and forces at play]

## Decision
[The decision made]

## Consequences
[Resulting context, both positive and negative]
```

## Status Transitions

- **Proposed** → **Accepted**: After review and approval
- **Accepted** → **Superseded by ADR-N**: When replaced by newer decision
- **Accepted** → **Deprecated**: When no longer relevant

### Supersede vs Amend

- **Supersede** (`-s`): New decision *replaces* the old entirely
- **Amend** (`-a`): New decision *clarifies or extends* without replacing

## Proposing Changes to Accepted ADRs

If your implementation would contradict an accepted ADR:

1. **Stop** - Don't implement the contradicting approach
2. **Discuss** - Explain the conflict to the user
3. **Draft new ADR** - If user agrees to change, create a superseding ADR
4. **Get approval** - User must accept before implementation proceeds

ADRs are immutable once accepted. Create new ADRs to change decisions.
