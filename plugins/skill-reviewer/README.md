# Skill Reviewer Plugin

Review SKILL.md files for quality, completeness, and best practices.

## Overview

The Skill Reviewer plugin provides a comprehensive checklist for auditing SKILL.md files. It checks frontmatter fields, content structure, file integrity, and anti-patterns against established conventions.

## Skills

### skill-reviewer

**Trigger phrases:** "review a skill", "audit skill quality", "check a SKILL.md"

**What it covers:**
- Frontmatter validation (name, description, allowed-tools, user-invocable, etc.)
- Content structure and progressive disclosure
- File reference integrity
- Anti-pattern detection
- Upstream spec compliance

**Usage:**
```
/skill-reviewer path/to/skill
```

## Agents

### skill-reviewer

A subagent for programmatic skill audits. Invoke via the Task tool for isolated reviews - receives a skill path and returns findings in the checklist's output format.

## Installation

```
/plugin marketplace add tvishwanadha/teja-skills
/plugin install skill-reviewer
```

## License

MIT
