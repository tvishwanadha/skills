# Default Skill Review Rules

## Frontmatter Checklist

### `name`
- Lowercase letters, numbers, and hyphens only; max 64 characters
- Must match the containing directory name
- If omitted, directory name is used

### `description`
- Non-empty; include keywords for auto-invocation (Claude matches user intent against descriptions)
- Describes both **what** the skill does and **when** to use it

### `disable-model-invocation`
- Should be `true` for side-effect workflows (deploy, commit, send, publish)
- If absent and the skill has side effects, flag it

### `user-invocable`
- Should be `false` only for background-knowledge skills (guidelines, context)
- If absent, default is `true` - verify that makes sense

### `allowed-tools`
- Only list tools the skill actually needs - no over-permissive access
- Use comma-space delimiter: `Read, Grep, Glob` (Claude Code convention)
- Flag if powerful tools (Bash, Write, Edit) are listed but not clearly needed
- Guide skills (`user-invocable: false`) and skills that use only MCP tools do not need `allowed-tools`

### `context`
- `fork` only makes sense with explicit task instructions, not guideline-only content
- If set to `fork`, verify the skill gives actionable steps (not just reference material)

### `argument-hint`
- If the skill accepts arguments, this should be present with a bracket-notation hint (e.g. `[issue-number]`)
- Verify `$ARGUMENTS` or positional substitutions (`$0`, `$1`, etc. - 0-based) are used in the body if hint is present

### `model`
- Optional override for which Claude model to use when skill is active
- Should be a valid model ID (e.g. `claude-sonnet-4-5-20250929`)

### `agent`
- Only relevant when `context: fork` is set
- Valid values: `Explore`, `Plan`, `general-purpose`, or a custom subagent name
- Defaults to `general-purpose` if omitted

### `hooks`
- Optional YAML object for hooks scoped to the skill's lifecycle
- Verify hook configuration follows the hooks specification format

### Invocation model coherence
- The combination of `user-invocable`, `disable-model-invocation`, and `description` should make sense together
- A skill that is not user-invocable AND has model invocation disabled would never trigger - flag this

## Content Checklist

### Size and structure
- Under 500 lines total; if over, should reference supporting files instead of inlining everything
- Progressive disclosure: metadata ~100 tokens, instructions <5000 tokens, detailed reference in separate files

### Instructions
- Step-by-step instructions present (not just a wall of text)
- Examples of inputs and expected outputs where applicable
- Edge cases addressed or acknowledged

### Argument handling
- If `argument-hint` is set, `$ARGUMENTS` or positional substitutions (`$0`, `$1` - 0-based index) must appear in the body
- `$ARGUMENTS[N]` and `$N` are equivalent (e.g. `$0` = first arg, `$1` = second)
- If `$ARGUMENTS` is used, behavior when no argument is provided should be defined

### Supporting files
- Referenced from SKILL.md with relative paths
- Organized in subdirectories (`reference/`, `scripts/`, `assets/`) if more than 2-3 files
- File references should be one level deep - avoid deeply nested reference chains

## Integrity Checklist (verify with Glob/Read)

### File references
- All file paths mentioned in SKILL.md (e.g., `[guide](reference/guide.md)`, `Read reference/foo.md`) resolve to actual files
- Use Glob to check existence of each referenced path relative to the SKILL.md directory

### Preprocessor commands
- `` !`command` `` directives reference commands that would be available in a typical environment
- Check that referenced scripts exist and paths are correct

### Script files
- Files in `scripts/` should be documented (what they do, any dependencies)

## Anti-patterns (flag if found)

| Anti-pattern | Why it's bad |
|---|---|
| Vague one-line description ("Helps with X") | Poor auto-invocation matching, unclear scope |
| Auto-invocation enabled on side-effect workflows | Risk of unintended destructive actions |
| Over 500 lines with no supporting files | Hard to maintain, slow to load |
| Over-permissive `allowed-tools` | Unnecessary security surface |
| `context: fork` with guideline-only content | Wastes a fork on passive reference material |
| Missing examples or success criteria | User can't verify correct behavior |
| Domain assumptions without explanation | Breaks portability across projects |
| Hardcoded absolute paths | Not portable |
| Unused frontmatter fields | Confusing, may indicate copy-paste from template |
