# Establish Composite Proxy Pattern for Service Boundary Abstraction: Transaction Boundaries Explicitly

These rules are ALWAYS ACTIVE for all service boundary implementations, API proxy patterns, client-to-service communication layers, and integration test infrastructure within the codebase.

### Rules

- **R-TXN-001** MUST: Transaction boundaries MUST be explicitly defined and testable through the proxy interface.
- **R-TXN-002** MUST: All service boundary implementations in `packages/client/src/runtime` MUST use `createCompositeProxy` or extend the composite proxy pattern.
- **R-TXN-003** MUST: All service proxies MUST implement consistent error handling for database-specific errors (referential actions, foreign key constraints, schema validation).
- **R-TXN-004** MUST: Integration tests MUST validate service boundary behavior across all supported database backends (PostgreSQL, SQLite, SQL Server).
- **R-TXN-005** SHOULD: Service proxy implementations SHOULD document the composition strategy including which concerns are composed and in what order.
- **R-TXN-006** SHOULD: Consider creating a service proxy generator or template to reduce boilerplate and ensure consistency.

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
- Service proxies implement consistent error handling for database-specific errors
- Verification commands execute successfully in CI pipeline

<enforcement>
Claude Code MUST NOT skip or defer verification of composite proxy pattern compliance. All service boundary implementations MUST be validated against these rules before approval. Violations MUST be escalated to the architecture review board or documented as approved exceptions.
</enforcement>