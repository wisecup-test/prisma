# Establish Composite Proxy Pattern for Service Boundary Abstraction: Service Proxies Extend

These rules are ALWAYS ACTIVE for all service boundary implementations, API proxy patterns, client-to-service communication layers, and integration test infrastructure within the codebase.

### Rules

- **R-PROXY-001** MAY: Service proxies MAY extend the base composite proxy pattern with additional cross-cutting concerns specific to their domain.

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

<enforcement>
Claude Code MUST NOT skip or defer verification of composite proxy pattern compliance. All service boundary implementations MUST be checked against these rules during code review and CI pipeline execution.
</enforcement>