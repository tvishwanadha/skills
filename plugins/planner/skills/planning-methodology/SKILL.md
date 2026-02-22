---
name: planning-methodology
description: >-
  This skill should be loaded during plan mode, when creating a plan,
  implementation planning, architecture design, feature breakdown, task
  breakdown, project planning, or solution design. Provides enhanced planning
  methodology with complexity scoring, sub-problem decomposition, and plan
  review.
user-invocable: false
---

# Planning Methodology

Enhanced workflow for the built-in plan mode. Augments each planning phase with complexity scoring, structured decomposition, and plan review.

## Phase 1: Initial Understanding

After launching Explore agents to understand the codebase:

### Score Complexity

Score the task across 7 dimensions (1-10 each) based on exploration results:

| Dimension | 1-3 (Low) | 4-6 (Moderate) | 7-10 (High) |
|-----------|-----------|-----------------|--------------|
| **Scope** | 1-3 files, single component | 4-9 files, 2-3 components | 10+ files, cross-cutting |
| **Novelty** | Extending existing pattern | Adapting known pattern to new context | New pattern, no existing precedent |
| **Dependencies** | No external integration | 1-2 integration points, well-documented | 3+ integrations, unclear interfaces |
| **Ambiguity** | Clear requirements, obvious approach | Some open questions, 2-3 viable approaches | Underspecified, requires research or user clarification |
| **Risk** | Easily reversible, local impact | Moderate blast radius, testable | Hard to reverse, wide blast radius, affects shared state |
| **Concurrency** | Sequential, no shared state | Some async/parallel, manageable state | Complex state management, race conditions possible |
| **Domain** | Standard CRUD/glue code | Moderate algorithmic complexity | Advanced algorithms, specialized domain knowledge |

Compute the composite score as a weighted average (Scope and Novelty at 1.5x):

```
composite = (scope*1.5 + novelty*1.5 + dependencies + ambiguity + risk + concurrency + domain) / 8.0
```

Present the assessment to the user:

```
**Complexity**: N/10 (level) - 1-line rationale

| Dimension | Score | Rationale |
|-----------|-------|-----------|
| Scope | N | brief note |
| Novelty | N | brief note |
| Dependencies | N | brief note |
| Ambiguity | N | brief note |
| Risk | N | brief note |
| Concurrency | N | brief note |
| Domain | N | brief note |
```

### Enhanced Research

Instruct Explore agents to also:

- Search for relevant skills via the Skill tool - try invoking skills that might provide domain context (e.g., security skills for auth work, testing skills for test-related changes)
- Use WebSearch (if available) for best practices and known pitfalls in the problem domain when the task involves unfamiliar patterns or technologies

## Phase 2: Design

### Simple Tasks (Composite 1-3)

Proceed with a direct plan. No decomposition needed.

### Moderate Tasks (Composite 4-6)

- If any dimension scored >= 7, consider decomposing into sub-problems
- Otherwise, proceed with a single detailed plan

### Complex Tasks (Composite 7+, or any dimension >= 8)

Decompose into sub-problems:

1. Break the task into 2-5 sub-problems with clear boundaries
2. Launch parallel `Plan` agents (one per sub-problem), each receiving:
   - Sub-problem scope and boundaries
   - Research context gathered in Phase 1
   - Brief descriptions of sibling sub-problems (for interface awareness)
3. Each sub-plan should include:
   - Approach and rationale
   - Files to modify (verified against codebase)
   - Ordered implementation steps
   - Interfaces with sibling sub-problems
   - Risks and mitigations

### Plan Output Format

Include at the top of the plan file:

```
**Complexity**: N/10 (level) - 1-line rationale
```

If decomposed, include sub-problem sections with clear interface descriptions between them.

## Phase 3: Review

Before calling ExitPlanMode:

1. Invoke `planner:review-plan` via the Skill tool, passing the plan file path
2. If critical or high findings exist, address them before proceeding
3. Present review findings alongside the plan for user approval

Skip plan review for simple tasks (composite 1-3) unless the user requests it.
