# Architecture Decision Records Guide

Background on what ADRs are, when to create them, and how to manage them over time. Content synthesized from [adr.github.io](https://adr.github.io/).

## What Are ADRs?

**Architectural Decisions (ADs)** are justified design choices that address functionally or non-functionally significant requirements. **Architecture Decision Records (ADRs)** capture individual decisions with their rationale, forming a project's decision log.

ADRs document *why* choices were made, not just what was chosen. This preserves knowledge for future team members and stakeholders who need to understand the reasoning behind the system's design.

## When to Create an ADR

Create an ADR for **architecturally significant** decisions - those that:

- Affect the structure, non-functional characteristics, dependencies, interfaces, or construction techniques of the system
- Are hard to reverse or change later
- Have long-term implications for the project
- Involve significant tradeoffs between competing concerns

**Don't create ADRs for:**
- Routine implementation choices
- Decisions that are easily reversible
- Standard patterns with no meaningful alternatives in your context

### Architectural Significance Test

Ask yourself:
1. Does this decision affect the system's structure or key quality attributes?
2. Would reversing this decision be costly or difficult?
3. Would a new team member benefit from understanding why this choice was made?
4. Does it involve external dependencies with unpredictable behavior?
5. Does it have cross-cutting impact across multiple parts of the system?
6. Is this something the team has never built before?

If yes to any of these, consider an ADR. For a full 7-criterion test, see [Ozimmer's ASR Test](https://www.ozimmer.ch/practices/2020/09/24/ASRTestECSADecisions.html).

## Template Formats

### Nygard Format (Default)

The original ADR format proposed by Michael Nygard in 2011. Simple and effective:

- **Title**: Short noun phrase (e.g., "Use PostgreSQL for persistence")
- **Status**: Proposed, Accepted, Deprecated, Superseded by ADR-N, or Amended by ADR-N
- **Context**: The forces at play - technical, political, social, project-specific
- **Decision**: The response to the forces, stated in full sentences
- **Consequences**: The resulting context after applying the decision

### Y-Statement Format

A concise single-sentence format:

> "In the context of [use case], facing [concern], we decided for [option] and neglected [alternatives] to achieve [quality], accepting [downside]."

Useful for quick capture; can be expanded into full Nygard format later.

### MADR (Markdown ADR)

An extended format that adds:
- Considered options with pros/cons
- Decision outcome explanation
- Links to related decisions

More comprehensive but heavier. Nygard format is sufficient for most projects.

## Managing ADRs Over Time

### Immutability Principle

Once an ADR is Accepted, don't modify its content - treat it as a historical record. If the decision needs to change:
- Create a new ADR that **supersedes** the old one
- Update the old ADR's status to "Superseded by ADR-N"

### When to Supersede vs Amend

**Supersede** when:
- The original decision is no longer valid
- You're taking a fundamentally different approach
- The context has changed enough that the old rationale doesn't apply

**Amend** when:
- Clarifying ambiguity in the original decision
- Adding implementation details that don't change the core decision
- Extending scope without contradicting the original

## Further Reading

- [Writing good ADRs](writing-adrs.md) - section-by-section guidance, anti-patterns, START/ecADR checklists
- [Michael Nygard's original blog post](http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions) (2011)
- [adr-tools](https://github.com/npryce/adr-tools) - canonical CLI reference for status transitions and linking conventions
- [ADR GitHub organization](https://adr.github.io/) - templates, tooling, practices
- [ADR templates collection](https://adr.github.io/adr-templates/) - community-maintained template formats
- [ADR practices posts](https://adr.github.io/ad-practices/) - best practices from adr.github.io maintainers
