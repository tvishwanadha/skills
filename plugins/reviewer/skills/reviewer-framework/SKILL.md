---
name: reviewer-framework
description: >-
  Shared review methodology for all reviewer agents and core review skills.
  Defines confidence scoring, severity levels, output format, and coalescing
  rules.
user-invocable: false

---

# Reviewer Framework

Shared conventions for all review types in the reviewer plugin. This skill is preloaded by reviewer agents and defines the common methodology.

## Confidence Scoring

Rate each finding on a 0-100 scale:

| Range | Meaning | Example |
|-------|---------|---------|
| 90-100 | Certain - verifiable fact or clear spec violation | Missing required field, broken file reference |
| 70-89 | High confidence - strong evidence, minor ambiguity | Likely bug based on control flow analysis |
| 50-69 | Moderate - reasonable concern, context-dependent | Naming inconsistency that may be intentional |
| 30-49 | Speculative - possible issue, needs human judgment | Potential performance concern |
| 0-29 | Low - stylistic preference or uncertain observation | Alternative approach suggestion |

**Reporting threshold**: Only findings with confidence >= 80 appear in the final report. Return all findings with scores - the orchestrator handles filtering.

## Severity Levels

- **Critical** - Must fix. Causes failures, data loss, security vulnerabilities, or spec violations that prevent correct operation.
- **High** - Should fix. Degrades quality, safety, portability, or maintainability significantly.
- **Medium** - Consider fixing. Meaningful improvement with moderate effort. The code works but could be better.
- **Low** - Minor. Style suggestions, best-practice nudges, or small readability improvements.

## Output Format

Structure each finding as:

```
[SEVERITY] Category: Brief description (confidence: N)
  File: <path>:<line>
  Details: What's wrong and why it matters
  Suggestion: Specific fix or improvement
```

Group findings by severity (critical first, then high, medium, low).

After all findings, include a summary line:

```
Total: N findings (X critical, Y high, Z medium, W low)
```

## Review Methodology

When reviewing code or content:

1. **Understand scope** - read the target files and understand their purpose before flagging issues
2. **Check against loaded rules** - apply the specific review rules (from defaults and/or local extensions)
3. **Verify claims** - use Glob/Grep/Read to confirm issues rather than guessing
4. **Score conservatively** - when uncertain, lower the confidence score rather than omitting the finding
5. **Be specific** - every finding must reference a file and line number, describe the issue concretely, and suggest a fix
6. **Avoid false positives** - do not flag intentional patterns as issues; if unsure, lower confidence

## Deduplication

When multiple review types flag the same issue:

- Keep the finding with the highest confidence score
- If confidence is equal, keep the one with the most specific suggestion
- Merge context from duplicates into the kept finding's details
- Note which review types identified the issue

## Coalescing Rules

The orchestrator combines findings from parallel review tasks:

1. Collect all findings from all review types
2. Deduplicate (see above)
3. Filter by confidence threshold (>= 80)
4. Sort by severity (critical > high > medium > low), then by confidence (descending)
5. Present the unified report
