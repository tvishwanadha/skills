# Default Plan Review Rules

Apply each category to the plan under review.

## Completeness

- All requirements from the original request are addressed
- Edge cases and error scenarios are considered
- No orphaned references (files, functions, or concepts mentioned but never defined)
- Implementation steps are specific enough to act on without ambiguity

## Feasibility

- Proposed file modifications target files that actually exist (verify with Glob/Grep)
- Referenced functions, classes, and patterns exist in the codebase
- No circular dependencies introduced
- Changes are compatible with the existing architecture and framework version

## Risk Coverage

- Breaking changes are identified and migration steps provided
- Testing strategy is defined (what to test, how to verify)
- Rollback approach is clear for non-trivial changes
- Side effects on existing functionality are called out

## Architectural Alignment

- Follows existing project patterns and conventions (naming, structure, abstractions)
- Does not introduce unnecessary new patterns when existing ones suffice
- Consistent with the project's dependency management approach
- Respects existing module boundaries

## Coherence

- If decomposed into sub-plans, they integrate cleanly at boundaries
- Implementation sequencing is correct (dependencies built before dependents)
- The plan as a whole addresses the original request
- No contradictions between sections
