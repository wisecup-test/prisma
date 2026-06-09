# Standardize Error-First Data Access Pattern with Type-Safe Query Validation: Type Level Query

These rules are ALWAYS ACTIVE for all database adapter implementations, query builders, database adapter code, transaction and batch operation handlers, type-level query validators, integration tests for error scenarios, and client-facing query APIs.

### Rules

- **R-30-006** SHOULD: Type-level query validation SHOULD be benchmarked to ensure compile-time overhead remains acceptable for large schemas

### Verify

```bash
# Verify no generic Error throws in adapter code
grep -r "throw new Error" packages/adapter-*/src --exclude-dir=node_modules | wc -l | awk '{if ($1 > 0) exit 1}'

# Verify typed error classes exist
grep -r "class.*Error extends" packages/adapter-*/src/errors.ts | wc -l | awk '{if ($1 < 3) exit 1}'

# Verify type checking and integration tests pass
npm run type-check && npm run test:integration -- --grep="error|validator|referential"

# Verify error integration tests exist
find packages/client/src/__tests__/integration/errors -name "test.ts" | wc -l | awk '{if ($1 < 5) exit 1}'
```

**Accept when:**
- All database adapters define typed error classes extending a common base error hierarchy, with zero generic Error throws in adapter code
- Type validation tests pass for all query patterns including nested relations, batch operations, and referential actions
- Integration tests demonstrate consistent error handling across all supported database backends (PostgreSQL, MySQL, SQLite, SQL Server, MongoDB)
- Type benchmark tests show compile-time overhead remains under defined thresholds for representative schema sizes
- Code review confirms new data access code follows error-first pattern with typed errors and compile-time validation

<enforcement>
Claude Code MUST NOT skip or defer verification. All verify commands MUST pass before accepting changes to data access layer code. Type checking and integration tests are mandatory on every pull request.
</enforcement>