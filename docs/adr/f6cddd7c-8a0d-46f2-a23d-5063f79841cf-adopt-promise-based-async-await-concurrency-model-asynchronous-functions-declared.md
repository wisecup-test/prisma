# Adopt Promise-Based Async/Await Concurrency Model: Asynchronous Functions Declared

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all TypeScript/JavaScript code within the codebase. All asynchronous operations must follow the Promise-based async/await concurrency model.

## Context

- The codebase is built on TypeScript/JavaScript runtime environments (Node.js, browser) that natively support Promise-based asynchronous programming
- Modern JavaScript applications require non-blocking I/O operations for database queries, HTTP requests, and file system operations to maintain performance and responsiveness
- The pattern was detected across 14 files with 90.82% confidence, spanning client runtime handlers, database adapters, test suites, and build scripts
- Legacy callback-based patterns and synchronous blocking operations create maintenance burden and reduce code readability
- The async/await syntax provides superior error handling through try-catch blocks and enables sequential-looking code for asynchronous operations

## Problem Statement

Without a standardized concurrency model, the codebase risks inconsistent asynchronous patterns including callback hell, mixed Promise and callback styles, unhandled promise rejections, and difficult-to-maintain control flow. This creates technical debt, increases bug surface area, and makes the codebase harder to understand and extend.

## Decision

1. MUST: All asynchronous functions MUST be declared with the async keyword and return Promises

## Policy Block

- MUST All asynchronous functions MUST be declared with the async keyword and return Promises

In scope:
- All TypeScript and JavaScript source files in packages/
- All test files using async operations
- All build and automation scripts requiring I/O operations
- Database adapter implementations
- HTTP request handlers and middleware
- File system operations

Out of scope:
- Third-party dependencies with callback-based APIs (must be wrapped)
- Legacy code scheduled for deprecation
- Synchronous utility functions that perform no I/O

Exceptions:
- EXC-001: Interfacing with third-party libraries that only provide callback-based APIs
- EXC-002: Performance-critical hot paths where Promise overhead is measured and significant

## Rationale

- Pattern detected across 14 files with 90.82% confidence indicates strong existing adoption and team familiarity
- Async/await provides superior readability and maintainability compared to callback chains or raw Promise chains
- Native TypeScript and JavaScript support eliminates need for external dependencies or transpilation overhead
- Error handling with try-catch blocks is more intuitive and consistent with synchronous code patterns
- Modern tooling (ESLint, TypeScript compiler) provides excellent support for detecting unhandled promises and type-checking async flows

## Consequences

Positive:
- Improved code readability with sequential-looking asynchronous code
- Consistent error handling patterns across the entire codebase
- Better TypeScript type inference for async operations and return types
- Reduced cognitive load when reading and maintaining asynchronous code
- Easier to reason about control flow and data dependencies

Negative:
- Developers unfamiliar with async/await require training on proper usage and common pitfalls
- Potential for unintentional sequential execution when parallel execution would be more efficient
- Stack traces can be less informative for async errors without proper tooling configuration
- Top-level await restrictions in some module systems may require workarounds

## Alternatives

- Continue using mixed callback and Promise patterns throughout the codebase (rejected)
  Rejected because: Creates inconsistent code style, increases maintenance burden, and makes error handling unpredictable. The detection of a strong existing pattern (90.82% confidence) indicates the team has already moved away from this approach.
  When valid: Never recommended for new code
- Use raw Promise chains with .then()/.catch() instead of async/await (rejected)
  Rejected because: Less readable for complex control flow, harder to debug, and more prone to errors like forgetting to return promises in chains. Async/await provides syntactic sugar that improves maintainability.
  When valid: Only for simple single-promise operations where async function overhead is unjustified
- Adopt reactive programming with RxJS Observables for all async operations (rejected)
  Rejected because: Introduces significant learning curve, adds heavy dependency, and is overkill for most use cases in the codebase. Better suited for complex event streams rather than simple async operations.
  When valid: Consider for specific modules dealing with complex event streams or real-time data

## Risks

- Developers may forget to await promises, causing race conditions and unhandled rejections
  Mitigation: Enable ESLint rules @typescript-eslint/no-floating-promises and @typescript-eslint/require-await. Configure TypeScript strict mode. Add pre-commit hooks to catch violations.
  Owner: Engineering team
- Sequential await calls may cause performance degradation when operations could run in parallel
  Mitigation: Establish code review guidelines to identify parallelizable operations. Provide training on Promise.all() patterns. Add performance monitoring for critical paths.
  Owner: Tech leads and code reviewers
- Legacy callback-based code may not be properly migrated, creating inconsistent patterns
  Mitigation: Create migration guide and automated refactoring scripts. Schedule technical debt sprints to systematically update legacy code. Track migration progress in technical debt backlog.
  Owner: Engineering team and product management

## Implementation Notes

- Configure ESLint with @typescript-eslint/no-floating-promises and @typescript-eslint/require-await rules to catch common mistakes
- Use Promise.all() for independent parallel operations and Promise.allSettled() when you need all results regardless of failures
- Wrap third-party callback-based APIs using util.promisify() in Node.js or manual Promise constructors
- Document async functions with @throws JSDoc tags to communicate error conditions to callers
- Consider using Promise.race() for timeout patterns and Promise.any() for fallback strategies

## Continuation Context


Verify commands:
- grep -r '\.then(' --include='*.ts' --include='*.js' packages/ | grep -v node_modules | wc -l
- npx eslint --rule '@typescript-eslint/no-floating-promises: error' packages/
- grep -r 'async.*function\|function.*async' --include='*.ts' packages/ | wc -l

Accept when:
- ESLint reports zero violations of no-floating-promises rule across the codebase
- Code review checklist includes verification that parallel async operations use Promise.all()
- At least 90% of async operations use async/await syntax rather than .then() chains (measured by grep ratio)
- All new pull requests pass automated checks for proper async/await usage

## Enforcement

- Verified by: ESLint pre-commit hooks checking @typescript-eslint/no-floating-promises and @typescript-eslint/require-await
- Verified by: CI pipeline runs ESLint with async/await rules as blocking checks
- Verified by: Code review checklist includes async/await pattern verification
- Verified by: TypeScript compiler strict mode catches missing await in many cases
- Violation handling: Pre-commit hooks block commits with floating promises or improper async usage
- Violation handling: CI builds fail if ESLint detects violations of async/await rules
- Violation handling: Code reviewers request changes for any callback-based patterns in new code
- Violation handling: Technical debt tickets created for legacy code violations discovered during maintenance
- Exception process: Developer documents exception justification in code comments with EXC-001 or EXC-002 reference
- Exception process: Tech lead reviews and approves exception with written rationale
- Exception process: Exception is logged in architecture decision log with expiration date for review
- Exception process: Performance-related exceptions require benchmark data and approval from architecture review board