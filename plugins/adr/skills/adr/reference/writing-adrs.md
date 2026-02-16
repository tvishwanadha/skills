# Writing Good ADRs

Content quality guidance for drafting ADR sections. Synthesized from [Olaf Zimmermann's ADR practices](https://ozimmer.ch/) and [adr.github.io](https://adr.github.io/).

## Section-by-Section Guidance

### Title

Use a short noun phrase that summarizes the decision — not a question.

- Good: "Use gVisor for sandbox isolation"
- Bad: "What should we use for isolation?"

### Context

Describe the forces at play without bias toward any option:
- What problem are you solving?
- What constraints exist?
- What quality attributes matter most?
- What alternatives exist, and what are their tradeoffs?

Discuss at least two approaches — this is where evidence belongs (prior experience, PoC results, expert input). Cover perspectives of developers, operators, maintainers, and end users.

### Decision

State what you decided, not what you considered.
- Be declarative: "We will use X"
- Include enough detail to be actionable

### Consequences

Document both positive and negative outcomes:
- What becomes easier?
- What becomes harder?
- What new problems might arise?
- Optionally, note when to revisit the decision (e.g., "re-evaluate after X" or "revisit if Y changes").

---

## Justification Quality

### Strong Justifications

- "We applied this pattern successfully on comparable projects with similar requirements"
- "PoC results demonstrated convincing outcomes aligned with required qualities"
- "Qualified engineers with this technology are available, supporting long-term staffing"

### Weak Justifications (avoid)

- "Everybody does it" — appeal to popularity without evidence
- "We've always done it this way" — historical inertia
- "I don't know other options" — insufficient research
- Resume-driven decisions or vendor pitches without substantiation

---

## Anti-Patterns

### Content Anti-Patterns

| Anti-pattern | What it looks like |
|---|---|
| **Fairy tale** | Only pros listed; includes truisms and tautologies |
| **Sales pitch** | Exaggeration, unsubstantiated adjectives, bragging |
| **Free lunch coupon** | Ignores consequences, especially long-term ones |
| **Dummy alternative** | Unrealistic options presented to favor a preferred solution |

### Scope Anti-Patterns

| Anti-pattern | What it looks like |
|---|---|
| **Sprint/rush** | Single option considered; ignores mid/long-term effects |
| **Tunnel vision** | Isolated context; neglects operational/maintenance perspectives |
| **Maze** | Topic/content mismatch; discussion derails into irrelevant details |

### Size Anti-Patterns

| Anti-pattern | What it looks like |
|---|---|
| **Blueprint/policy** | Commanding tone; excessive implementation detail for a decision record |
| **Mega-ADR** | Multiple pages stuffed with detailed design, diagrams, code snippets |
| **Novel/epic** | Entire architecture document compressed into a single ADR |

### Process Anti-Patterns

| Anti-pattern | What it looks like |
|---|---|
| **Decision-free ADR** | Describes a problem without stating a decision |
| **Option-free ADR** | States a decision without showing alternatives were considered |
| **Consequence-free ADR** | No tradeoffs documented |
| **Stale ADR** | Never updated; no superseding records created when decisions change |
| **Over-documentation** | ADRs for trivial, easily reversible decisions |
| **Magic tricks** | False urgency, problem-solution mismatch, pseudo-accuracy with weighted scoring |

---

## Further Reading

- [How to create ADRs — and how not to](https://www.ozimmer.ch/practices/2023/04/03/ADRCreation.html) — 7 good practices and 11 anti-patterns
- [Architecture Decision Making](https://www.ozimmer.ch/practices/2020/04/27/ArchitectureDecisionMaking.html) — justification frameworks and Y-statements
- [Definition of Ready for ADs](https://ozimmer.ch/practices/2023/12/01/ADDefinitionOfReady.html) — START criteria deep dive
- [Definition of Done for ADs](https://ozimmer.ch/practices/2020/05/22/ADDefinitionOfDone.html) — ecADR framework
