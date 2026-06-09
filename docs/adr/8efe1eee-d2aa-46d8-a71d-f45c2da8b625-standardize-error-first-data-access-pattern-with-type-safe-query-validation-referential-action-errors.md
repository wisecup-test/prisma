# Standardize Error-First Data Access Pattern with Type-Safe Query Validation: Referential Action Errors

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all data access layer implementations, query builders, and database adapter code. It applies to both runtime query execution and compile-time type validation.

## Context

- The codebase exhibits a consistent pattern of error handling in data access operations across 31 files with 92.48% confidence, indicating a deliberate architectural choice
- Evidence shows integration of type-safe validators, referential integrity enforcement, and batch transaction error handling across multiple database adapters (PostgreSQL, SQLite, SQL Server)
- The pattern appears in test files, adapter implementations, and type benchmark suites, suggesting it is a cross-cutting concern that affects both runtime behavior and compile-time type safety
- Database operations require robust error handling for constraint violations, foreign key errors, and transaction failures while maintaining type safety across the query API
- The pattern supports multiple database backends with consistent error semantics, requiring a unified approach to error representation and propagation

## Problem Statement

Data access operations in multi-database environments face challenges in providing consistent error handling, type-safe query validation, and referential integrity enforcement across different database backends. Without a standardized pattern, error handling becomes inconsistent, type safety is compromised, and developers must handle database-specific error cases differently, leading to fragile code and runtime failures that could be caught at compile time.

## Decision

1. SHOULD: Referential action errors (onDelete, onUpdate) SHOULD include the affected entity, constraint name, and suggested resolution

## Policy Block

- SHOULD Referential action errors (onDelete, onUpdate) SHOULD include the affected entity, constraint name, and suggested resolution

In scope:
- All database adapter implementations (PostgreSQL, MySQL, SQLite, SQL Server, MongoDB)
- Query builder and query validation logic
- Transaction and batch operation handlers
- Type-level query validators and type benchmarks
- Integration tests for error scenarios and referential actions
- Client-facing query APIs and result types

Out of scope:
- Application-level business logic errors (handled by application layer)
- Network and connection errors (handled by connection pool layer)
- Schema migration errors (handled by migration tooling)
- Authentication and authorization errors (handled by security layer)

Exceptions:
- EXC-001: Legacy database adapters that cannot be immediately refactored may temporarily use generic errors
- EXC-002: Performance-critical hot paths where type validation overhead is measured to exceed 100ms compile time

## Rationale

- Pattern detected across 31 files with 92.48% confidence indicates this is an established, proven approach that has been validated through extensive use
- Type-safe error handling at the data access layer catches entire classes of bugs at compile time, reducing runtime failures and improving developer experience
- Consistent error normalization across database backends enables portable application code that doesn't need backend-specific error handling logic
- Integration of validators with batch operations and referential actions demonstrates that this pattern successfully handles complex real-world scenarios including transactions and constraint enforcement

## Consequences

Positive:
- Developers get immediate compile-time feedback on invalid queries, reducing debugging time and runtime errors
- Consistent error handling across database backends simplifies application code and enables easier database migration
- Structured error metadata enables better error reporting, logging, and monitoring in production systems
- Type-safe query validation prevents entire classes of SQL injection and query construction bugs
- Batch operations provide clear error context, making it easier to implement retry logic and partial failure handling

Negative:
- Compile-time type validation adds overhead to TypeScript compilation, particularly for large schemas (mitigated by benchmarking per R-30-006)
- Maintaining error normalization across multiple database backends requires ongoing effort as new databases are added
- Learning curve for developers unfamiliar with type-level programming and advanced TypeScript features
- Error type hierarchy must be carefully designed to avoid breaking changes as new error cases are discovered

## Alternatives

- Runtime-only error handling without compile-time type validation (rejected)
  Rejected because: Fails to catch query construction errors at compile time, leading to runtime failures that could be prevented. Evidence shows the codebase has invested heavily in type-level validation (type-benchmark-tests), indicating this approach was considered and rejected.
  When valid: May be appropriate for dynamically-typed languages or prototypes where compile-time validation is not feasible
- Database-specific error handling without normalization (rejected)
  Rejected because: Forces application code to handle backend-specific error cases, making database migration difficult and violating DRY principle. Evidence shows adapters for multiple databases (pg, sqlite, sqlserver), indicating multi-backend support is a requirement.
  When valid: Acceptable for single-database applications with no plans for migration or multi-backend support
- Monadic error handling (Result<T, E> types) instead of exceptions (deferred)
  Rejected because: Not rejected, but deferred for future consideration. Would provide more explicit error handling but requires significant API changes and may not integrate well with existing Promise-based async patterns.
  When valid: Could be adopted in future major version if ecosystem moves toward functional error handling patterns

## Risks

- Type-level validation complexity may become unmaintainable as schema size grows, leading to excessive compile times or type system limitations
  Mitigation: Implement continuous benchmarking of type validation performance (R-30-006). Establish compile-time budget thresholds. Consider incremental type checking strategies for very large schemas.
  Owner: Type System Team
- New database backends may have error semantics that don't map cleanly to the normalized error hierarchy, requiring breaking changes
  Mitigation: Design error hierarchy with extension points (R-30-008). Document error mapping strategy for each adapter. Maintain compatibility test suite across all backends.
  Owner: Database Adapter Team
- Developers may bypass type-safe query APIs using raw SQL or dynamic queries, undermining the safety guarantees
  Mitigation: Provide clear documentation on when raw queries are appropriate. Implement linting rules to flag unsafe query patterns. Ensure raw query APIs still use typed error handling.
  Owner: Developer Experience Team

## Implementation Notes

- Start by defining a base error class hierarchy with common properties (code, message, metadata). Extend for specific error types (ConstraintViolation, ForeignKeyError, TransactionError).
- Implement error normalization in each database adapter by mapping native database error codes to the common error hierarchy. Maintain a mapping table for each supported database.
- Use TypeScript conditional types and template literal types to implement compile-time query validation. Leverage type-level recursion for nested query validation.
- For batch operations, wrap each operation result in a discriminated union type (Success<T> | Failure<E>) to enable type-safe error handling without losing batch context.
- Add integration tests for each error scenario across all supported databases to ensure consistent behavior. Include tests for referential actions, constraint violations, and transaction failures.
- Document error handling patterns with examples in developer guides. Provide migration guide for codebases adopting this pattern.

## Continuation Context


Verify commands:
- grep -r "throw new Error" packages/adapter-*/src --exclude-dir=node_modules | wc -l | awk '{if ($1 > 0) exit 1}'
- grep -r "class.*Error extends" packages/adapter-*/src/errors.ts | wc -l | awk '{if ($1 < 3) exit 1}'
- npm run type-check && npm run test:integration -- --grep="error|validator|referential"
- find packages/client/src/__tests__/integration/errors -name "test.ts" | wc -l | awk '{if ($1 < 5) exit 1}'

Accept when:
- All database adapters define typed error classes extending a common base error hierarchy, with zero generic Error throws in adapter code
- Type validation tests pass for all query patterns including nested relations, batch operations, and referential actions
- Integration tests demonstrate consistent error handling across all supported database backends (PostgreSQL, MySQL, SQLite, SQL Server, MongoDB)
- Type benchmark tests show compile-time overhead remains under defined thresholds for representative schema sizes
- Code review confirms new data access code follows error-first pattern with typed errors and compile-time validation

## Enforcement

- Verified by: Automated CI pipeline runs type checking and integration tests on every pull request
- Verified by: ESLint rules flag usage of generic Error class in data access layer code
- Verified by: Code review checklist includes verification of typed error handling and query validation
- Verified by: Type benchmark tests run in CI to detect compile-time performance regressions
- Verified by: Integration test suite validates error behavior across all database adapters
- Violation handling: CI build fails if type checking errors are detected or integration tests fail
- Violation handling: Pull requests with generic Error usage in adapters are automatically flagged for review
- Violation handling: Compile-time performance regressions beyond threshold trigger build warnings and require justification
- Violation handling: Quarterly audits review error handling patterns and identify technical debt for remediation
- Exception process: Developer submits exception request with justification (performance, legacy constraints, etc.) to architecture team
- Exception process: Architecture team reviews with relevant domain experts (performance team for compile-time issues, adapter team for backend-specific concerns)
- Exception process: Approved exceptions are documented in code comments with ADR reference and expiration date
- Exception process: Exception registry is reviewed quarterly to track exceptions and plan remediation