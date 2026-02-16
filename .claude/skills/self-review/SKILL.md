---
name: self-review
description: >-
  Review all plugins in this marketplace. Use when asked to audit plugins, run a
  self-review, check marketplace health, or validate plugin quality across the
  repository.
user-invocable: true
allowed-tools: Read, Glob, Task
argument-hint: "[plugin-name (optional)]"
---

# Self-Review: Marketplace Audit

Orchestrate a full review of this marketplace: plugins, local skills, local agents, and documentation. Spawns one isolated subagent per plugin, reviews local assets directly, then synthesizes a unified health report.

## Procedure

### 1. Read the registry

Read `.claude-plugin/marketplace.json` at the repo root. Parse the `plugins` array to get the list of registered plugins (name, source path, description).

If `$ARGUMENTS` is provided and non-empty, filter the plugin list to only the plugin whose name matches. If no match is found, report an error and stop.

### 2. Pre-flight checks

These checks require cross-plugin context, so the orchestrator handles them directly:

- **Source paths exist**: For each registered plugin, verify the `source` path resolves to an existing directory using Glob.
- **Unregistered plugins**: Glob for `plugins/*/` directories and check each against the registry. Flag any plugin directories not registered in `marketplace.json`.
- **Orphaned entries**: Flag any registry entries whose source path does not exist on disk.

Record all pre-flight findings for the final report.

### 3. Discover local skills and agents

Glob for `.claude/skills/*/SKILL.md` and `.claude/agents/*.md` to build the list of local assets to review.

### 4. Spawn all review tasks

Launch **all tasks in parallel** (single message, multiple Task calls):

**Plugin reviews** - for each plugin that passed pre-flight, launch a Task with:
- `subagent_type`: `"plugin-reviewer"`
- `prompt`: the absolute plugin path, the marketplace description from the registry, and the absolute repo root path

**Local skill reviews** - for each local skill, launch a Task with:
- `subagent_type`: `"general-purpose"`
- `prompt`: instruct the agent to read the skill review checklist at `<repo-root>/plugins/skill-reviewer/skills/skill-reviewer/SKILL.md` (using the absolute repo root path), then review the local skill at the given absolute path. Return findings in the checklist's output format (Summary, Issues, Checklist Results, Recommendations).

**Local agent reviews** - for each local agent, launch a Task with:
- `subagent_type`: `"general-purpose"`
- `prompt`: instruct the agent to read the agent format in `<repo-root>/.claude/skills/plugins-guide/SKILL.md` (using the absolute repo root path), then review the agent at the given absolute path. Check: valid frontmatter (name, description, tools), skills references resolve to existing local skills, body provides clear instructions, filename matches name field. Return findings as: Summary, Issues (with severity), Recommendations.

**Documentation review** - launch one Task with:
- `subagent_type`: `"general-purpose"`
- `prompt`: instruct the agent to read the repo-level docs (`CLAUDE.md`, `AGENTS.md`, `README.md`, `plugins/README.md`) and `.claude-plugin/marketplace.json`, then check for:
  - `AGENTS.md` guide skills table lists all local skills in `.claude/skills/`
  - `AGENTS.md` directory layout and conventions are accurate
  - `CLAUDE.md` references `AGENTS.md` and has no stale instructions
  - `README.md` accurately describes the marketplace
  - `plugins/README.md` lists all registered plugins with descriptions consistent with `marketplace.json`
  - Flag missing docs, stale references, or inconsistencies between docs and actual repo state
  - Return findings as: Summary, Issues (with severity), Recommendations

Wait for all tasks to complete and collect their outputs. If a task fails, include the error in the report under the relevant section and degrade the overall status accordingly.

### 5. Synthesize the report

Combine pre-flight results and all task outputs into a unified report:

```
# Marketplace Health Report

**Overall Status: Healthy | Needs Attention | Major Issues**
**Date: <current date>**

## Registry Checks
- Source paths: PASS | <details>
- Unregistered plugins: PASS | <list>
- Orphaned entries: PASS | <list>

## Plugin Reviews

| Plugin | Quality | Structure | Manifest | Skills | Agents | Cross-Refs |
|--------|---------|-----------|----------|--------|--------|------------|
| name   | Good    | PASS      | PASS     | PASS   | N/A    | PASS       |

<full subagent output for each plugin>

## Local Skill Reviews

| Skill | Quality |
|-------|---------|
| name  | Good    |

<full output for each local skill>

## Documentation Review

<full documentation review output>

## Cross-Cutting Observations
- <patterns, inconsistencies, or shared issues across all reviewed assets>

## High Priority
1. <most impactful across everything>
2. ...

## Low-Hanging Fruit
1. <quick wins across everything>
2. ...
```

**Overall status rules:**
- **Healthy** - all plugins and local skills Good, docs consistent, no pre-flight failures
- **Needs Attention** - any asset rated Needs Work, or minor pre-flight/doc issues
- **Major Issues** - any asset rated Major Issues, or critical failures
