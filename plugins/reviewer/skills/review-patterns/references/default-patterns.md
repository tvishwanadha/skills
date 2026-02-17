# Default Patterns Review Rules

## Naming Conventions

- **Internal consistency** - names within a module follow the same style (camelCase, snake_case, etc.); flag mixed styles
- **Descriptive names** - variables, functions, and types convey purpose; flag single-letter names outside tight loops or well-known conventions (i, j, k, x, y)
- **Boolean naming** - boolean variables/functions use is/has/can/should prefixes or equivalent idiomatic patterns
- **Abbreviation consistency** - abbreviations are either always expanded or always abbreviated within the module (not mixed)
- **Convention alignment** - names follow language idioms (PascalCase for types in Go/TS, snake_case for functions in Python/Rust)

## Structural Patterns

- **Single responsibility** - functions/classes/modules have one clear purpose; flag functions over ~50 lines or with multiple unrelated responsibilities
- **Abstraction level consistency** - functions mix high-level orchestration with low-level details; each function should operate at one level
- **Dependency direction** - higher-level modules depend on lower-level ones, not the reverse; flag circular imports or upward dependencies
- **Configuration vs. hardcoding** - magic numbers, hardcoded URLs, or embedded credentials that should be configurable
- **Dead code** - unused imports, unreachable functions, commented-out blocks left in place

## Style Consistency

- **Formatting within file** - consistent indentation, brace style, and whitespace usage (flag when inconsistent with the rest of the file)
- **Import organization** - imports grouped by convention (stdlib, external, internal) with consistent ordering
- **Comment style** - consistent comment format within the module; comments explain "why" not "what"
- **Error message style** - consistent capitalization, punctuation, and detail level in user-facing messages

## Test Quality

- **Test naming** - test names describe the scenario and expected outcome, not just the function name
- **Assertion quality** - tests make specific assertions; flag tests that only check "no error" without verifying behavior
- **Test isolation** - tests do not depend on execution order or shared mutable state
- **Edge case coverage** - tests cover boundary values, error paths, and empty/null inputs - not just the happy path
- **Test-code ratio** - significant new logic should have corresponding tests; flag untested public interfaces

## Module Organization

- **File size** - files over ~500 lines that could be split into focused modules
- **Export surface** - modules export only what consumers need; flag large public APIs with unused exports
- **Colocation** - related code (type + helpers + tests) lives together rather than scattered across the project
- **Index/barrel files** - re-export files that obscure the actual source; flag when they complicate navigation
