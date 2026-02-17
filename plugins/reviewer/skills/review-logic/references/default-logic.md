# Default Logic Review Rules

## Control Flow

- **Unreachable code** - code after unconditional returns, breaks, or throws
- **Missing branches** - switch/match without default/exhaustive handling; if without else where both paths have different effects
- **Infinite loops** - loops without clear termination conditions or break paths
- **Off-by-one errors** - loop bounds, array indexing, range calculations
- **Short-circuit evaluation** - reliance on evaluation order that may not hold across refactoring

## Edge Cases

- **Null/undefined/nil handling** - operations on values that could be absent without guards
- **Empty collections** - code that assumes non-empty arrays, maps, or strings
- **Boundary values** - zero, negative numbers, max integers, empty strings, single-element collections
- **Concurrent access** - shared state modified without synchronization where concurrency is possible
- **Unicode and encoding** - string operations that assume ASCII or single-byte characters

## Error Handling

- **Swallowed errors** - catch blocks that silently ignore exceptions or log without re-raising when callers expect errors to propagate
- **Error type specificity** - catching broad exception types (Exception, Error) when specific types are appropriate
- **Resource cleanup** - files, connections, or locks opened without corresponding cleanup in error paths (finally/defer/with)
- **Error message quality** - errors that lose context (original cause, relevant state) making debugging harder
- **Inconsistent error patterns** - mixing return codes, exceptions, and result types within the same module

## State Management

- **Mutation of shared state** - modifying globals, singletons, or passed-by-reference objects without clear ownership
- **Stale state** - using cached or computed values after the source data may have changed
- **Initialization order** - dependencies between initialization steps that could fail if reordered
- **State machine violations** - transitions that skip required intermediate states or lack guards

## Data Integrity

- **Type coercion** - implicit conversions that may lose data (float to int, string to number)
- **Overflow/underflow** - arithmetic operations that could exceed type bounds
- **Injection vulnerabilities** - user input reaching SQL, shell, or template engines without sanitization
- **Serialization round-trips** - data that may not survive serialize/deserialize cycles (dates, BigInt, custom types)

## Logical Consistency

- **Contradictory conditions** - branches that can never both be true but are treated as independent
- **Redundant checks** - conditions that are always true/false given prior control flow
- **Assumption violations** - code comments or docs state one invariant but code permits violations
- **API contract mismatches** - callers pass arguments that violate documented preconditions
