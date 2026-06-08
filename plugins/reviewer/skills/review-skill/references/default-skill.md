# Default Skill Review Rules

For any frontmatter field not listed below, check the upstream spec before flagging - this checklist lags the specs.

## Frontmatter Checklist

### Standard - required

#### `name`
- 1-64 characters; lowercase letters, numbers, and hyphens only; no leading, trailing, or consecutive hyphens
- Must match the containing directory name

#### `description`
- Non-empty; max 1024 characters
- Describes both **what** the skill does and **when** to use it; include keywords so agents can match it against user intent

### Standard - optional

#### `license`
- License name, or a reference to a bundled license file

#### `compatibility`
- Max 500 characters; states environment requirements (intended product, system packages, network access)
- Most skills omit it; flag only when the skill clearly has requirements that go undocumented

#### `metadata`
- Map of string keys to string values for properties outside the spec
- Prefer reasonably unique key names to avoid collisions with other clients

#### `allowed-tools`
- List of pre-approved tools the skill may use; experimental, support varies by harness
- Only list tools the skill actually needs - flag powerful tools (Bash, Write, Edit) listed without clear need
- Prefer a comma-space delimiter (`Read, Grep, Glob`); both the standard and Claude Code accept it
- Guide skills (`user-invocable: false`) and skills using only MCP tools do not need this field

### Claude Code extensions (Claude Code skills only)

#### `disable-model-invocation`
- Should be `true` for side-effect workflows (deploy, commit, send, publish)
- If absent and the skill has side effects, flag it

#### `user-invocable`
- Should be `false` only for background-knowledge skills (guidelines, context)
- If absent, default is `true` - verify that makes sense

#### `context`
- `fork` only makes sense with explicit task instructions, not guideline-only content
- If set to `fork`, verify the skill gives actionable steps (not just reference material)

#### `argument-hint`
- If the skill accepts arguments, present with a bracket-notation hint (e.g. `[issue-number]`)
- The body must then use `$ARGUMENTS` or positional substitutions (`$0`, `$1`, ... - 0-based; `$ARGUMENTS[N]` and `$N` are equivalent)
- Define the behavior when no argument is provided

#### `model`
- Optional override for which model to use when the skill is active
- Should be a valid model ID for the harness

#### `agent`
- Only relevant when `context: fork` is set
- Valid values: `Explore`, `Plan`, `general-purpose`, or a custom subagent name; defaults to `general-purpose`

#### `hooks`
- Optional YAML object for hooks scoped to the skill's lifecycle
- Verify hook configuration follows the hooks specification format

#### Invocation model coherence
- The combination of `user-invocable`, `disable-model-invocation`, and `description` should make sense together
- A skill that is not user-invocable AND has model invocation disabled would never trigger - flag this

## Content Checklist

### Size and structure
- Under 500 lines total; if over, reference supporting files instead of inlining everything
- Progressive disclosure: metadata ~100 tokens, instructions <5000 tokens, detailed reference in separate files

### Instructions
- Step-by-step instructions present (not just a wall of text)
- Examples of inputs and expected outputs where applicable
- Edge cases addressed or acknowledged

### Supporting files
- Referenced from SKILL.md with relative paths
- Organized in subdirectories (`references/`, `scripts/`, `assets/`) if more than 2-3 files
- File references should be one level deep - avoid deeply nested reference chains

## Integrity Checklist (verify with Glob/Read)

### File references
- All file paths mentioned in SKILL.md (e.g., `[guide](references/guide.md)`, `Read references/foo.md`) resolve to actual files
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
