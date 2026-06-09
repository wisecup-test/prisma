# Establish Composite Proxy Pattern for Service Boundary Abstraction: Service Proxies Database

These rules are ALWAYS ACTIVE for all service boundary implementations, API proxy patterns, client-to-service communication layers, and integration test infrastructure within the codebase.

### Rules

- **R-SPD-001** SHOULD: Service proxies SHOULD be database-agnostic and support multiple backend implementations (PostgreSQL, SQLite, SQL Server) through the same interface.
- **R-SPD-002** MUST: All service boundary implementations in packages/client/src/runtime MUST use createCompositeProxy or extend the composite proxy pattern.
- **R-SPD-003** MUST: Service proxies MUST implement consistent error handling for database-specific errors (referential actions, foreign key constraints, schema validation).
- **R-SPD-004** MUST: Integration tests MUST validate service boundary behavior across all supported database backends (PostgreSQL, SQLite, SQL Server).
- **R-SPD-005** SHOULD: Service proxy composition strategy SHOULD be documented for each service proxy including which concerns are composed and in what order.
- **R-SPD-006** MAY: Consider creating a service proxy generator or template to reduce boilerplate and ensure consistency.

### Verify

```bash
# Count composite proxy usage
grep -r "createCompositeProxy" packages/client/src --include="*.ts" | wc -l

# Find all service proxy implementations
find packages/client/src -name "*Proxy.ts" -type f -exec grep -l "export.*Proxy" {} \;

# Run integration tests for service boundaries
npm test -- --testPathPattern="integration.*service.*boundary" --passWithNoTests
```

**Accept when:**
- All service boundary implementations in packages/client/src/runtime use createCompositeProxy or extend the composite proxy pattern
- Integration tests exist for service boundaries covering error handling, transactions, and multi-database scenarios
- No direct service instantiation bypasses the proxy layer except in documented exception cases (EX-23-001, EX-23-002)
- Service proxy composition strategies are documented

<enforcement>
Claude Code MUST NOT skip or defer verification. Violations result in CI build failure and code review blocking until proxy pattern compliance is demonstrated or an approved exception is granted.
</enforcement>