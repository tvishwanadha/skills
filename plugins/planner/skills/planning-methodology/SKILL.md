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

Enhanced workflow for the built-in plan mode. Uses a draft-score-design loop: load project constraints early, draft a lightweight plan, score complexity against the draft, then flesh out the full design.

## Phase 1: Understanding

### Load Project Constraints

Before launching `Explore` subagents, try to invoke `local-plan-standards` via the Skill tool. If found, extract its rules as planning constraints - these may inform what to explore (e.g., design patterns to follow, architectural boundaries, conventions to verify). If not found, proceed without constraints.

### Explore and Research

Launch `Explore` Task subagents to understand the codebase, informed by any loaded constraints. Instruct them to also:

- Search for relevant skills via the Skill tool - try invoking skills that might provide domain context (e.g., security skills for auth work, testing skills for test-related changes)
- Use WebSearch (if available) for best practices and known pitfalls in the problem domain when the task involves unfamiliar patterns or technologies

## Phase 2: Draft Plan

Launch a `Plan` Task subagent to create a lightweight structural plan for the full task. Provide it with:
- The task description and research context from Phase 1
- Project constraints loaded in Phase 1 (if any)

The draft should include:
- High-level approach (1-2 sentences)
- Key files to modify (verified against codebase)
- Rough ordered steps (one line each)
- Any project constraints that apply

This draft is not the final plan - its purpose is to give complexity scoring a concrete artifact to assess.

## Phase 3: Score Complexity

Score the task across 7 dimensions (1-10 each) based on the draft plan. The draft is intentionally lightweight and will understate rough edges - when uncertain, round up:

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
**Complexity**: N/10 - 1-line rationale

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

## Phase 4: Design

Flesh out the draft into a detailed plan using `Plan` Task subagents. First, classify the task tier to determine whether to decompose.

### Decomposition

Classify the tier, checking from most to least complex:

1. **Complex** if composite >= 7 or any single dimension >= 8. Decompose.
2. **Moderate** if composite >= 4 or any single dimension >= 7. Consider decomposing, especially if multiple dimensions scored >= 6.
3. **Simple** otherwise. Skip decomposition.

When decomposing, aim for sub-problems that would individually score below 5. If that granularity isn't achievable, still decompose as far as practical - any reduction in complexity helps. Only skip decomposition if the task is truly indivisible. Launch parallel `Plan` subagents for decomposed sub-problems, or a single `Plan` subagent otherwise.

### Plan Subagent Inputs

Every `Plan` subagent receives:
- The draft plan from Phase 2 (or the relevant sub-problem scope if decomposed)
- Research context gathered in Phase 1
- Project constraints loaded in Phase 1 (if any)
- Brief descriptions of sibling sub-problems, if decomposed (for interface awareness)

### Expected Output

Each plan should include: approach, files to modify (verified against codebase), ordered implementation steps, interfaces with sibling sub-problems (if decomposed), and risks.

Include at the top of the plan file:

```
**Complexity**: N/10 (tier) - 1-line rationale
```

### Synthesis (decomposed plans only)

After all parallel `Plan` subagents complete, synthesize sub-plans into a single plan:

1. Verify interfaces between sub-problems are consistent (shared files, data formats, ordering dependencies)
2. Resolve conflicts where sub-plans make incompatible assumptions
3. Establish a global implementation order - which sub-problems can be implemented in parallel and which have sequential dependencies

## Phase 5: Review

Before calling ExitPlanMode:

1. Invoke `planner:review-plan` via the Skill tool, passing the plan file path
2. If critical or high findings exist, address them before proceeding
3. Present review findings alongside the plan for user approval
