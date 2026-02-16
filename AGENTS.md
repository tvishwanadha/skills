# Agent Guide

This repository is a personal plugin marketplace - a collection of reusable skills, agents, and plugins for AI coding assistants.

## Directory Layout

```
├── plugins/                        # Published plugins (one directory per plugin)
│   └── <plugin-name>/
│       ├── .claude-plugin/plugin.json
│       ├── skills/
│       └── agents/
├── .claude/
│   ├── skills/                     # Local skills
│   │   ├── plugins-guide/          # Plugin conventions (guide skill)
│   │   ├── skills-guide/           # Skill conventions (guide skill)
│   │   └── self-review/            # Marketplace audit skill
│   └── agents/                     # Local agent definitions
│       └── plugin-reviewer.md      # Single-plugin review subagent
├── .claude-plugin/
│   └── marketplace.json            # Marketplace registry
├── CLAUDE.md                       # Project instructions
├── AGENTS.md                       # This file - agent guidance
├── README.md                       # Marketplace overview
└── LICENSE
```

## Guide Skills

This repository uses **guide skills** - background-knowledge reference skills that the agent should consult automatically when working on related topics. Guide skills follow the `*-guide` naming convention and are marked `user-invocable: false`.

Available guide skills (in `.claude/skills/`):

| Skill | When to consult |
|-------|-----------------|
| [`skills-guide`](.claude/skills/skills-guide/SKILL.md) | Creating or modifying SKILL.md files, reviewing skill quality, understanding frontmatter fields |
| [`plugins-guide`](.claude/skills/plugins-guide/SKILL.md) | Creating or modifying plugins, writing plugin.json manifests, marketplace registration |

**Always read the relevant guide skill before making changes.** These are not optional - they define the conventions and quality bar for this repository. Consult them during planning to align your approach, and refer back to them while implementing to avoid mistakes.

## Conventions

**Plugins**: each plugin lives in `plugins/<name>/` with a `.claude-plugin/plugin.json` manifest. Read [`plugins-guide`](.claude/skills/plugins-guide/SKILL.md) for the full directory structure, manifest schema, and naming rules.

**Skills**: each skill is a `SKILL.md` file in its own directory. Frontmatter defines metadata; body defines instructions. Read [`skills-guide`](.claude/skills/skills-guide/SKILL.md) for authoring conventions, the review checklist, and anti-patterns to avoid.

**Agents**: agent definitions are Markdown files in `agents/` directories (within plugins or `.claude/agents/`). They configure subagents with tools, skills, and system prompts.

## Quality Bar

Good contributions are concise, well-structured, and follow existing patterns. Skills have clear descriptions for auto-invocation, appropriate tool permissions, and step-by-step instructions. Plugins include a manifest with all required fields and at least one skill. Guide skills document the detailed criteria.

## Marketplace Registration

When adding a new plugin, register it in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) and update [`plugins/README.md`](plugins/README.md). The marketplace file maps plugin names to their source directories.

## Testing

Run the `self-review` skill before committing to validate plugins, local skills, and documentation across the repository.

For single-plugin validation:

```
claude plugin validate plugins/<plugin-name>
```

## Upstream References

Guide skills contain upstream documentation URLs for edge cases. When a guide skill's inline content does not cover a specific question, follow the upstream references listed in that guide skill.
