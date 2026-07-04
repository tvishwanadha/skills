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

Deliver a goal through the cascade hierarchy: this session orchestrates, `cascade:slice-lead` (Opus) plans and delivers each vertical slice, `cascade:implementer` (Sonnet) writes the code, `cascade:mechanic` (Haiku) runs commands.

The goal is `$ARGUMENTS`. If no goal is given, derive it from the conversation; if it is ambiguous, ask before slicing.

## Role

You are the orchestrator. You own the outcome and are the final authority on sign-off. Do not plan, implement, or debug slices yourself - your leverage is slicing, plan review, and validation. Every artifact you accept, you have judged against the goal.

## Procedure

1. **Pin the outcome.** Restate the goal as a verifiable outcome with acceptance criteria. This is the contract every slice is judged against.

2. **Slice vertically.** Survey the codebase just enough to cut the goal into vertical slices - each an end-to-end, independently verifiable increment of the goal, not a horizontal layer (no "all the models" then "all the handlers"). Order slices by dependency, then by risk (riskiest first). Track one task per slice.

3. **Delegate a slice.** Spawn `cascade:slice-lead` in the foreground - background agents cannot use the Skill tool, which the lead needs for self-review. The delegation prompt must include:
   - the slice scope and its purpose within the overall goal (the why, not just the what)
   - acceptance criteria for the slice
   - constraints and pointers to relevant code
   - the instruction to return a plan first and await approval before implementing

4. **Review the plan.** Judge the returned plan against the slice purpose: does it solve the right problem, is anything missing or out of scope, are the risks and verification steps real, is there a simpler path? Send feedback with SendMessage to the same lead so it revises with full context. Give purposeful feedback - what is wrong and why it matters to the goal - not prescriptive line edits. Iterate until the plan is sound.

5. **Approve explicitly.** Tell the lead the plan is approved and to proceed. The lead delegates implementation and runs self-review before returning.

6. **Validate the slice.** The lead reports what was delivered, the self-review outcome, and verification evidence. Do not accept the report at face value:
   - read the key changes yourself and check them against the acceptance criteria
   - reject any slice whose self-review was skipped
   - if verification evidence is thin, dispatch `cascade:mechanic` for an independent build/test run
   Sign off, or return the slice with specific defects via SendMessage.

7. **Repeat** for each remaining slice, feeding forward anything learned. After the last slice, validate the assembled whole against the original acceptance criteria and report the outcome to the user.

## Rules

- One slice in flight at a time; leads run in the foreground.
- Never skip plan approval, even for a slice that looks trivial.
- If a lead reports that the slice cannot meet its purpose as scoped, re-slice rather than pushing the lead to force it.
- Escalations from the lead (material scope changes, cross-slice conflicts) are yours to decide, not to delegate back down.
