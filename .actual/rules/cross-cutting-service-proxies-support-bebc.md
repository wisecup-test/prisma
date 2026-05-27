# Establish Composite Proxy Pattern for Service Boundary Abstraction: Service Proxies Support

These rules are ALWAYS ACTIVE for all service boundary implementations, API proxy patterns, client-to-service communication layers, and integration test infrastructure within the codebase.

### Rules

- **R-SVC-PROXY-001** MUST: Service proxies MUST support transparent error propagation including referential action errors and foreign key constraint violations.
- **R-SVC-PROXY-002** MUST: All service boundary implementations in `packages/client/src/runtime` MUST use `createCompositeProxy` or extend the composite proxy pattern.
- **R-SVC-PROXY-003** MUST: Service proxies MUST implement consistent error handling for database-specific errors (referential actions, foreign key constraints, schema validation).
- **R-SVC-PROXY-004** SHOULD: Integration tests SHOULD validate service boundary behavior across all supported database backends (PostgreSQL, SQLite, SQL Server).
- **R-SVC-PROXY-005** SHOULD: Each service proxy composition strategy SHOULD be documented including which concerns are composed and in what order.
- **R-SVC-PROXY-006** MAY: Performance-critical paths MAY bypass the proxy layer when proxy overhead is measured and unacceptable (exception EX-23-002).

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
- Service proxies demonstrate transparent error propagation for referential actions and foreign key constraints

<enforcement>
Claude Code MUST NOT skip or defer verification. Violations result in CI build failure and code review block until compliance is demonstrated or an approved exception is granted.
</enforcement>