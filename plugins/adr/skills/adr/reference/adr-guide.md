# Architecture Decision Records Guide

This guide provides detailed context on what ADRs are, when to create them, and best practices. Content synthesized from [adr.github.io](https://adr.github.io/).

## What Are ADRs?

**Architectural Decisions (ADs)** are justified design choices that address functionally or non-functionally significant requirements. **Architecture Decision Records (ADRs)** capture individual decisions with their rationale, forming a project's decision log.

ADRs document *why* choices were made, not just what was chosen. This preserves knowledge for future team members and stakeholders who need to understand the reasoning behind the system's design.

## When to Create an ADR

Create an ADR for **architecturally significant** decisions—those that:

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

If yes to any of these, consider an ADR.

## Template Formats

### Nygard Format (Used by adrjs)

The original ADR format proposed by Michael Nygard in 2011. Simple and effective:

- **Title**: Short noun phrase (e.g., "Use PostgreSQL for persistence")
- **Status**: Proposed, Accepted, Deprecated, or Superseded
- **Context**: The forces at play—technical, political, social, project-specific
- **Decision**: The response to the forces, stated in full sentences
- **Consequences**: The resulting context after applying the decision

### Y-Statement Format

A concise single-sentence format:

> "In the context of [use case], facing [concern], we decided for [option] to achieve [quality], accepting [downside]."

Useful for quick capture; can be expanded into full Nygard format later.

### MADR (Markdown ADR)

An extended format that adds:
- Considered options with pros/cons
- Decision outcome explanation
- Links to related decisions

More comprehensive but heavier. Nygard format is sufficient for most projects.

## Best Practices

### Writing Good ADRs

1. **Title**: Use a noun phrase that summarizes the decision (not a question)
   - Good: "Use gVisor for sandbox isolation"
   - Bad: "What should we use for isolation?"

2. **Context**: Describe the forces at play without bias toward any option
   - What problem are you solving?
   - What constraints exist?
   - What quality attributes matter?

3. **Decision**: State what you decided, not what you considered
   - Be declarative: "We will use X"
   - Include enough detail to be actionable

4. **Consequences**: Document both positive and negative outcomes
   - What becomes easier?
   - What becomes harder?
   - What new problems might arise?

### Definition of Ready (START Criteria)

Before finalizing an ADR, ensure it has:
- **S**cope: Clear boundaries of what the decision covers
- **T**radeoffs: Documented pros/cons of the chosen approach
- **A**lternatives: Other options that were considered
- **R**ationale: Why this option was selected
- **T**iming: When the decision applies (now, future, conditional)

### Anti-Patterns to Avoid

- **Decision-free ADRs**: Describing a problem without stating a decision
- **Option-free ADRs**: Stating a decision without showing alternatives were considered
- **Consequence-free ADRs**: Not documenting the tradeoffs
- **Stale ADRs**: Never updating status or creating superseding records
- **Over-documentation**: Creating ADRs for trivial decisions

## Managing ADRs Over Time

### Immutability Principle

Once an ADR is Accepted, don't modify its content—treat it as a historical record. If the decision needs to change:
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

- [Michael Nygard's original blog post](http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions) (2011)
- [ADR GitHub organization](https://adr.github.io/) - templates, tooling, practices
- [adrjs documentation](https://github.com/phodal/adr) - the CLI tool used in this project
