# Standardize Error-First Data Access Pattern with Type-Safe Query Validation: Database Adapters Normalize

These rules are ALWAYS ACTIVE for all database adapter implementations, query builders, query validation logic, transaction and batch operation handlers, type-level query validators, integration tests for error scenarios, and client-facing query APIs across all supported database backends (PostgreSQL, MySQL, SQLite, SQL Server, MongoDB).

### Rules

- **R-30-001** MUST: Database adapters MUST normalize database-specific errors into a consistent error hierarchy that abstracts backend differences.
- **R-30-002** MUST: All database adapter implementations MUST define typed error classes extending a common base error hierarchy with zero generic Error throws in adapter code.
- **R-30-003** MUST: Query builder and query validation logic MUST implement compile-time type-safe validation using TypeScript conditional types and template literal types.
- **R-30-004** MUST: Transaction and batch operation handlers MUST wrap each operation result in a discriminated union type (Success<T> | Failure<E>) to enable type-safe error handling without losing batch context.
- **R-30-005** MUST: Integration tests MUST validate consistent error handling across all supported database backends for referential actions, constraint violations, and transaction failures.
- **R-30-006** SHOULD: Type validation performance SHOULD be continuously benchmarked to ensure compile-time overhead remains under defined thresholds for representative schema sizes.
- **R-30-007** SHOULD: Error handling patterns SHOULD be documented with examples in developer guides, including migration guides for codebases adopting this pattern.
- **R-30-008** SHOULD: Error hierarchy design SHOULD include extension points to accommodate new database backends without requiring breaking changes.

### Verify

```bash
# Verify no generic Error throws in adapter code
grep -r "throw new Error" packages/adapter-*/src --exclude-dir=node_modules | wc -l | awk '{if ($1 > 0) exit 1}'

# Verify typed error classes exist in all adapters
grep -r "class.*Error extends" packages/adapter-*/src/errors.ts | wc -l | awk '{if ($1 < 3) exit 1}'

# Verify type checking and integration tests pass
npm run type-check && npm run test:integration -- --grep="error|validator|referential"

# Verify integration error tests exist
find packages/client/src/__tests__/integration/errors -name "test.ts" | wc -l | awk '{if ($1 < 5) exit 1}'
```

**Accept when:**
- All database adapters define typed error classes extending a common base error hierarchy, with zero generic Error throws in adapter code
- Type validation tests pass for all query patterns including nested relations, batch operations, and referential actions
- Integration tests demonstrate consistent error handling across all supported database backends (PostgreSQL, MySQL, SQLite, SQL Server, MongoDB)
- Type benchmark tests show compile-time overhead remains under defined thresholds for representative schema sizes
- Code review confirms new data access code follows error-first pattern with typed errors and compile-time validation

<enforcement>
Claude Code MUST NOT skip or defer verification. All verify commands MUST pass before accepting changes to database adapter code, query builders, or error handling logic. CI build MUST fail if type checking errors are detected or integration tests fail. Pull requests with generic Error usage in adapters MUST be automatically flagged for review.
</enforcement>