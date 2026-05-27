# Establish Composite Proxy Pattern for Service Boundary Abstraction: Schema Validation Errors

These rules are ALWAYS ACTIVE for all service boundary implementations, API proxy patterns, client-to-service communication layers, and integration test infrastructure within the codebase.

### Rules

- **R-SVC-001** MUST: Schema validation errors MUST be detectable and handleable at the service boundary layer.
- **R-SVC-002** MUST: All service boundary implementations in `packages/client/src/runtime` MUST use `createCompositeProxy` or extend the composite proxy pattern.
- **R-SVC-003** MUST: All service proxies MUST implement consistent error handling for database-specific errors (referential actions, foreign key constraints, schema validation).
- **R-SVC-004** MUST: Integration tests MUST validate service boundary behavior across all supported database backends (PostgreSQL, SQLite, SQL Server).
- **R-SVC-005** SHOULD: Document the composition strategy for each service proxy including which concerns are composed and in what order.
- **R-SVC-006** SHOULD: Consider creating a service proxy generator or template to reduce boilerplate and ensure consistency.

### Verify

```bash
# Count composite proxy usage
grep -r "createCompositeProxy" packages/client/src --include="*.ts" | wc -l

# Find all proxy implementations
find packages/client/src -name "*Proxy.ts" -type f -exec grep -l "export.*Proxy" {} \;

# Run integration tests for service boundaries
npm test -- --testPathPattern="integration.*service.*boundary" --passWithNoTests
```

**Accept when:**
- All service boundary implementations in `packages/client/src/runtime` use `createCompositeProxy` or extend the composite proxy pattern
- Integration tests exist for service boundaries covering error handling, transactions, and multi-database scenarios
- No direct service instantiation bypasses the proxy layer except in documented exception cases (EX-23-001, EX-23-002)
- Schema validation errors are demonstrably detectable and handleable at service boundaries

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory for service boundary implementations. Violations must be caught during code review and CI pipeline validation.
</enforcement>