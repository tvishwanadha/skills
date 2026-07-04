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

2. **Slice vertically.** Survey the codebase just enough to cut the goal into vertical slices - each an end-to-end, independently verifiable increment of the goal, not a horizontal layer (no "all the models" then "all the handlers"). Order by dependency, then by risk (riskiest first). Track one task per slice. Never create a slice whose acceptance criteria you cannot state - resolve that ambiguity with the user first.

3. **Delegate a slice.** Spawn a `slice-lead` agent and wait for its plan. Send all follow-ups by resuming the same lead so it keeps its context; start a fresh lead (re-passing the slice brief and prior plan) only if resumption is not possible. The delegation prompt must include:
   - the slice scope and its purpose within the overall goal
   - acceptance criteria for the slice
   - constraints and pointers to relevant code
   - the instruction to return a plan first and await approval before implementing

4. **Review the plan.** Judge the returned plan against the slice purpose: does it solve the right problem, is anything missing or out of scope, are the risks and verification steps real, is there a simpler path? Send feedback to the same lead: what is wrong and why it matters to the goal, not line-level edits. Iterate until the plan is sound.

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
- If a lead reports the slice cannot meet its purpose as scoped, re-slice rather than pushing the lead to force it.
- Batch questions: collect ambiguities and conflicts and raise them to the user as one set, not one interrupt per discovery.
- Do not pause between slices to check in with the user - stop only for escalations needing a user decision, or final delivery.
- Progress must survive context loss: keep the slice task list current, and on resume trust the task list and version-control history over recollection. Never re-delegate a slice already signed off.
