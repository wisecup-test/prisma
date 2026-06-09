# Standardize Error-First Data Access Pattern with Type-Safe Query Validation: Adapters Provide Database

These rules are ALWAYS ACTIVE for all database adapter implementations, query builders, query validation logic, transaction and batch operation handlers, type-level query validators, integration tests for error scenarios, and client-facing query APIs across all supported database backends (PostgreSQL, MySQL, SQLite, SQL Server, MongoDB).

### Rules

- **R-30-001** MUST: All database adapters define typed error classes extending a common base error hierarchy with zero generic Error throws in adapter code.
- **R-30-002** MUST: Implement error normalization in each database adapter by mapping native database error codes to the common error hierarchy.
- **R-30-003** MUST: Use TypeScript conditional types and template literal types to implement compile-time query validation for all query patterns including nested relations and batch operations.
- **R-30-004** MUST: Wrap batch operation results in discriminated union types (Success<T> | Failure<E>) to enable type-safe error handling without losing batch context.
- **R-30-005** MUST: Add integration tests for each error scenario across all supported databases to ensure consistent behavior including referential actions, constraint violations, and transaction failures.
- **R-30-006** MUST: Implement continuous benchmarking of type validation performance with compile-time overhead remaining under defined thresholds for representative schema sizes.
- **R-30-007** MUST: Document error handling patterns with examples in developer guides and provide migration guide for codebases adopting this pattern.
- **R-30-008** MAY: Adapters MAY provide database-specific error extensions for advanced use cases while maintaining the common error interface.

### Verify

```bash
# Verify no generic Error throws in adapter code
grep -r "throw new Error" packages/adapter-*/src --exclude-dir=node_modules | wc -l | awk '{if ($1 > 0) exit 1}'

# Verify typed error classes exist
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
Claude Code MUST NOT skip or defer verification. All verify commands MUST pass before accepting changes to data access layer code. Type checking and integration tests are mandatory on every pull request. Violations trigger automated CI build failures and require architecture team review for exceptions.
</enforcement>