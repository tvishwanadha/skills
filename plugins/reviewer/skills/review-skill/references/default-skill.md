# Default Skill Review Rules

For any frontmatter field not listed below, check the upstream spec before flagging - this checklist lags the specs.

## Frontmatter Checklist

### Standard - required

#### `name`
- 1-64 characters; lowercase letters, numbers, and hyphens only; no leading, trailing, or consecutive hyphens
- Must match the containing directory name under the agentskills.io standard. Claude Code defaults `name` to the directory name and allows a plugin skill to diverge deliberately (e.g. `name: fancy` in `my-plugin/skills/review/SKILL.md` yields `/my-plugin:fancy`) - flag a mismatch only when it appears unintentional

#### `description`
- Non-empty; max 1024 characters. Claude Code caps `description` plus `when_to_use` at 1536 characters combined
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
- Prefer a space-separated string (`Read Grep Glob`) - the standard requires it; Claude Code also accepts commas or a YAML list
- Guide skills (`user-invocable: false`) and skills using only MCP tools do not need this field

### Claude Code extensions (Claude Code skills only)

Accept `yes`/`no`, `on`/`off`, `1`/`0` as well as `true`/`false` for boolean frontmatter fields - do not flag those forms as invalid.

#### `disallowed-tools`
- Lists tools removed while the skill is active
- Flag if a tool the skill's own instructions rely on is disallowed

#### `disable-model-invocation`
- Should be `true` for side-effect workflows (deploy, commit, send, publish)
- If absent and the skill has side effects, flag it

#### `user-invocable`
- Should be `false` only for background-knowledge skills (guidelines, context)
- If absent, default is `true` - verify that makes sense

#### `when_to_use`
- Extra invocation context, appended to `description`
- Shares the 1536-character combined cap with `description` - flag only if the combined total exceeds that

#### `paths`
- Glob patterns limiting when the skill activates
- Verify the patterns match the files or directories the skill's instructions describe

#### `context`
- `fork` only makes sense with explicit task instructions, not guideline-only content
- If set to `fork`, verify the skill gives actionable steps (not just reference material)

#### `background`
- Only meaningful with `context: fork` - flag if set without `fork`
- `false` waits for the result in-turn; verify that matches the skill's described behavior

#### `argument-hint`
- If the skill accepts arguments, present with a bracket-notation hint (e.g. `[issue-number]`)
- The body must then use `$ARGUMENTS` or positional substitutions (`$0`, `$1`, ... - 0-based; `$ARGUMENTS[N]` and `$N` are equivalent)
- Define the behavior when no argument is provided

#### `arguments`
- Named arguments enabling `$name` substitution in the body - a separate mechanism from the positional substitutions above
- Verify the body uses `$name` for each declared argument

#### `shell`
- `bash` (default) or `powershell`, for !`command` blocks
- Flag if set to anything else

#### `model`
- Optional override for which model to use when the skill is active
- Accepts the same values as `/model` - aliases `sonnet`, `opus`, `haiku`, `fable`, or a full model ID - or `inherit`; the override lasts for the rest of the current turn

#### `effort`
- `low`, `medium`, `high`, `xhigh`, or `max` - flag any other value
- Absent means the skill inherits the session setting

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

### Voice
- SKILL.md is read by the agent executing the skill, not a human browsing documentation - flag prose addressed to a human reader
- Flag sentences that justify or explain why a rule exists rather than stating what to do
- Flag reassurance language and meta-claims about the skill's coverage, completeness, or safety
- Flag examples that illustrate how a mechanism works rather than showing a direct action to take
- Flag hedged instructions that create an escape hatch with no decision rule ("if available", "where possible", "consider") - an instruction either states its condition or is unconditional
- Instructions should read as imperatives; flag second-person narrative standing in for a direct instruction

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
| Rationale prose diluting instructions | Wastes context, invites deviation |
