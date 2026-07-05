Always consult @AGENTS.md before making changes to this repository.

ALWAYS load skills via the Skill tool instead of reading SKILL.md files directly. Use `/skills` to list available skills.

Delegate implementation to the cascade `implementer` agent and command execution to the cascade `mechanic` agent - the main session plans, reviews, and decides; it does not write code or run builds and tests itself. Reserve direct shell use for trivial read-only one-liners.

When a plan converges - in plan mode or in conversation - hand execution to `cascade:orchestrate`.
