# Establish Composite Proxy Pattern for Service Boundary Abstraction: Integration Tests Validate

These rules are ALWAYS ACTIVE for all service boundary implementations, API proxy patterns, client-to-service communication layers, and integration test infrastructure within the codebase.

### Rules

- **R-PROXY-001** SHOULD: Integration tests SHOULD validate service boundary behavior across all supported database backends (PostgreSQL, SQLite, SQL Server).
- **R-PROXY-002** MUST: All service boundary implementations in `packages/client/src/runtime` MUST use `createCompositeProxy` or extend the composite proxy pattern.
- **R-PROXY-003** MUST: Service proxies MUST implement consistent error handling for database-specific errors including referential actions, foreign key constraints, and schema validation.
- **R-PROXY-004** SHOULD: Service proxy implementations SHOULD document the composition strategy including which concerns are composed and in what order.
- **R-PROXY-005** MUST: Integration tests MUST cover error handling, transactions, and multi-database scenarios for all service boundaries.
- **R-PROXY-006** MAY: Performance-critical paths MAY bypass the proxy layer only when proxy overhead is measured and documented as unacceptable (exception EX-23-002).
- **R-PROXY-007** MAY: Legacy service boundaries predating this ADR with no active development MAY be exempted (exception EX-23-001).

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
- CI pipeline integration tests validate service boundary behavior across all supported database backends

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated linting rules MUST check for composite proxy usage at service boundaries. Code review MUST verify proxy pattern compliance before merge. CI pipeline MUST fail if linting rules detect non-compliant service boundary patterns. Violations require either remediation or approved exception from architecture review board.
</enforcement>