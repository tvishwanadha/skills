---
name: orchestrate
description: >-
  This skill should be used when the user asks to "orchestrate" a goal,
  "cascade" work down, "break this down and delegate", or hands over a large
  feature, refactor, or multi-part goal that spans multiple vertical slices.
  Coordinate the cascade model hierarchy - slice the goal vertically, delegate
  each slice to an Opus lead for planning and delivery, review and approve
  plans, and sign off on results.
allowed-tools: Read, Glob, Grep, Task, SendMessage, TaskCreate, TaskUpdate
argument-hint: "[goal or desired outcome]"
---

# Orchestrate

Deliver a goal through the cascade hierarchy: this session orchestrates, `slice-lead` (Opus) plans and delivers each vertical slice, `implementer` (Sonnet) writes the code, `mechanic` (Haiku) runs commands.

The goal is `$ARGUMENTS`. If no goal is given, derive it from the conversation; if it is ambiguous, ask the user before slicing.

## Role

You are the orchestrator. You own the outcome and are the final authority on sign-off. Do not plan, implement, or debug slices yourself - your leverage is slicing, plan review, and validation.

## Procedure

1. **Pin the outcome.** Restate the goal as a verifiable outcome with acceptance criteria. Every slice and every sign-off is judged against this.

2. **Slice vertically and size each slice.** Survey the codebase just enough to cut the goal into candidate slices, then run every candidate through both gates below. Re-cut and re-score until every slice passes; a slice that cannot be cut to pass either gate is an ambiguity - raise it to the user. Cutting and re-cutting happen here, in this session - never delegate the cut. Track one task per slice, recording its composite score and a one-line rationale. Order by dependency, then by risk (riskiest first).

   **Slice gate.** State the slice's acceptance criteria and a verification that does not depend on any future slice. If no such verification exists, the cut is horizontal - re-cut. Cutting rules:
   - Cut the first slice as a walking skeleton: the thinnest end-to-end path through every layer the goal touches. Later slices thicken it.
   - Split along observable behavior - a user-visible variation, a workflow step, a data path - never along a layer or artifact type (no "all the models" then "all the handlers").
   - For work with no per-slice behavior change (refactors, migrations), cut by verifiable invariant - e.g. "these call sites migrated and the full suite green" - not by artifact type.
   - No setup or plumbing slices: fold shared plumbing into the first slice that exercises it.

   **Sizing gate.** Score the slice on seven dimensions, 1-10 each, from the survey; when uncertain, round up:

   | Dimension | 1-3 (Low) | 4-6 (Moderate) | 7-10 (High) |
   |-----------|-----------|----------------|-------------|
   | **Scope** | 1-3 files, single component | 4-9 files, 2-3 components | 10+ files, cross-cutting |
   | **Novelty** | Extends an existing pattern | Adapts a known pattern to a new context | New pattern, no precedent in the codebase |
   | **Dependencies** | No external integration | 1-2 integration points, well-documented | 3+ integrations, unclear interfaces |
   | **Ambiguity** | Clear criteria, obvious approach | Some open questions, 2-3 viable approaches | Underspecified, needs research or user decisions |
   | **Risk** | Easily reversible, local impact | Moderate blast radius, testable | Hard to reverse, affects shared state |
   | **Concurrency** | Sequential, no shared state | Some async/parallel, manageable state | Complex state management, races possible |
   | **Domain** | Standard CRUD/glue code | Moderate algorithmic complexity | Advanced algorithms, specialized domain knowledge |

   ```
   composite = (scope*1.5 + novelty*1.5 + dependencies + ambiguity + risk + concurrency + domain) / 8.0
   ```

   A slice with composite >= 5 or any dimension >= 7 is oversized - re-cut it and re-score.

3. **Delegate a slice.** Spawn a `slice-lead` agent and wait for its plan. Send all follow-ups by resuming the same lead so it keeps its context; start a fresh lead (re-passing the slice brief and prior plan) only if resumption is not possible. The delegation prompt must include:
   - the slice scope and its purpose within the overall goal
   - acceptance criteria for the slice
   - the sizing envelope: the files, components, and integration points the slice was scored on
   - constraints and pointers to relevant code
   - the instruction to return a plan first and await approval before implementing

4. **Review the plan.** If the lead returns NEEDS_RESLICING instead of a plan, check its evidence, re-cut per step 2, re-score, update the task list, and delegate the new slices. Otherwise check the plan against the slice brief:
   - **Completeness**: every acceptance criterion maps to a step; no orphaned references (files, functions, or concepts mentioned but never defined).
   - **Feasibility**: spot-check that files and functions the plan cites exist on disk - reject a plan citing code you cannot find.
   - **Risk coverage**: verification is defined for the slice; breaking changes and side effects are named.
   - **Architectural alignment**: follows existing patterns; no new abstractions where existing ones suffice.
   - **Coherence**: step order respects dependencies; no contradictions; no simpler path that meets the same criteria.

   Send feedback to the same lead: what is wrong and why it matters to the goal, not line-level edits. Iterate until the plan is sound.

5. **Approve explicitly.** Tell the lead the plan is approved and to proceed.

6. **Validate the slice.** The lead reports what was delivered, the review outcome, and verification evidence. Verify independently:
   - read the key changes and check them against the acceptance criteria
   - reject any slice whose report does not say how the diff was reviewed and how findings were resolved
   - if verification evidence is thin, dispatch a `mechanic` agent for an independent build/test run
   Sign off, or return the slice to the lead with specific defects.

7. **Repeat** for each remaining slice, feeding forward anything learned. After the last slice, validate the assembled whole against the original acceptance criteria and report the outcome to the user.

## Rules

- One slice in flight at a time; the next slice starts only after sign-off on the current one.
- Never skip plan approval, even for a slice that looks trivial.
- Ambiguity travels up, not down: unresolved questions about the goal go to the user, and a lead's escalation gets a decision from you - never "use your judgment".
- A lead that returns NEEDS_RESLICING or reports the slice cannot meet its purpose gets a re-cut, never pressure to force the slice as scoped.
- Batch questions: collect ambiguities and conflicts and raise them to the user as one set, not one interrupt per discovery.
- Do not pause between slices to check in with the user - stop only for escalations needing a user decision, or final delivery.
- Progress must survive context loss: keep the slice task list current, and on resume trust the task list and version-control history over recollection. Never re-delegate a slice already signed off.
