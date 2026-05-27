# Establish Composite Proxy Pattern for Service Boundary Abstraction: Service Boundary Abstractions

These rules are ALWAYS ACTIVE for all service boundary abstractions, API proxy patterns, client-to-service communication layers, and integration test infrastructure within the codebase.

### Rules

- **R-SBA-001** MUST: All service boundary abstractions MUST use the composite proxy pattern as implemented in `createCompositeProxy` for runtime behavior composition.
- **R-SBA-002** MUST: All client-to-service communication layers in `packages/client/src/runtime` MUST implement consistent error handling for database-specific errors (referential actions, foreign key constraints, schema validation).
- **R-SBA-003** MUST: Integration tests validating service boundaries MUST cover error handling, transactions, and multi-database scenarios (PostgreSQL, SQLite, SQL Server).
- **R-SBA-004** SHOULD: Service proxy implementations SHOULD document the composition strategy including which concerns are composed and in what order.
- **R-SBA-005** MAY: Consider creating a service proxy generator or template to reduce boilerplate and ensure consistency across implementations.

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
- Service proxy implementations document their composition strategy

<enforcement>
Claude Code MUST NOT skip or defer verification. All service boundary abstractions MUST comply with R-SBA-001 through R-SBA-005. Violations block CI builds and code review merges until remediated or approved exceptions are documented.
</enforcement>