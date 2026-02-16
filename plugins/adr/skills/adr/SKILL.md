---
name: adr
description: Architecture Decision Records - the project's design knowledge base. CONSULT ADRs before planning any feature or architectural change. Use when (1) planning implementation, (2) modifying core systems, (3) asked why something works a certain way, (4) making significant technical decisions. ADRs explain WHY choices were made.
allowed-tools: Glob, Grep, Read, Write, Edit
---

# Architecture Decision Records

ADRs are the project's architectural knowledge base. **Consult them before planning.**

## Quick Reference

| Status | Priority | Meaning |
|--------|----------|---------|
| **Accepted** | 1st | Active decision, governs implementation |
| **Superseded by ADR-N** | 2nd | Read superseding ADR, original has context |
| **Accepted + Amended by ADR-N** | 2nd | Read both original and amendment together |
| **Proposed** | 3rd | Follow as if accepted, but still open to revision |
| **Deprecated** | 4th | Historical only, no longer applies |

---

## Reading Priority

1. **Accepted** - Active decisions that govern current implementation
2. **Superseded by ADR-N** - Read the superseding ADR, but check original for context
3. **Accepted + Amended by ADR-N** - Read both the amendment and the original together
4. **Proposed** - Follow as if accepted; still malleable if issues arise
5. **Deprecated** - Historical context only, no longer applies

**Key principle**: If an ADR is superseded, the superseding ADR takes precedence, but the original often explains the journey.

---

## Before Planning (IMPORTANT)

**Always consult ADRs before implementing features or making changes.**

1. Glob for ADRs (`docs/adr/*.md`, or `**/adr/*.md` respecting `.gitignore`) and Grep their contents for keywords related to the subsystem you're modifying
2. Read ADRs related to the subsystem you're modifying - understand what decisions are already in place and what they affect
3. Follow accepted decisions unless explicitly asked to change them
4. If your plan contradicts an ADR, stop and discuss the conflict with the user before proceeding
5. **If your plan involves an architecturally significant decision, include drafting a new ADR as a step in the plan** - decisions that affect structure, are hard to reverse, or involve tradeoffs should be recorded

---

## Creating ADRs

Create an ADR when the decision:
- Affects system structure or key quality attributes
- Would be costly or difficult to reverse
- Involves tradeoffs between competing concerns
- Will guide future implementation choices

Draft the ADR as part of planning - writing Context and Consequences helps clarify the decision and surface which existing ADRs and subsystems are affected. It starts as Proposed and can be revised; don't wait until you have a perfect answer.

Follow the step-by-step procedure in [reference/creating-adrs.md](reference/creating-adrs.md). When drafting or reviewing an ADR, consult the [writing quality guide](reference/writing-adrs.md) for section guidance and anti-patterns to avoid.

**ADRs are immutable once accepted - never edit the content of an accepted ADR.** To change a decision, create a new ADR that supersedes or amends the original (only the old ADR's Status section is updated). If your implementation would contradict an accepted ADR, stop and get user approval before drafting a superseding ADR.

---

## Further Reading

- [ADR background](reference/adr-guide.md) - what ADRs are, when to create them, template formats
- [Writing good ADRs](reference/writing-adrs.md) - content quality and anti-patterns
