# Standardize Public API Configuration and Schema Export Patterns: Database Adapter Implementations

These rules are ALWAYS ACTIVE for all public API modules, client configuration files, database adapter implementations, schema management utilities, and integration test configurations within the Prisma ecosystem.

### Rules

- **R-ADAPTER-001** MUST: Database adapter implementations MUST provide consistent error handling interfaces that map provider-specific errors to standardized Prisma error types.

### Verify

```bash
# Verify standardized config file naming
grep -r 'prisma.config.ts' packages/ | wc -l

# Check adapter interface exports for error handling and type conversion
find packages/adapter-* -name 'errors.ts' -o -name 'conversion.ts' | xargs grep -l 'export.*Error\|export.*convert'

# Run integration tests for public API patterns
npm run test:integration -- --grep 'configuration|schema|adapter'
```

**Accept when:**
- All database adapter packages export standardized error handling and type conversion interfaces
- Configuration files follow naming conventions (prisma.config.ts, _schema.ts) across test suites and integration scenarios
- Public API contracts maintain backward compatibility as verified by integration test suite
- New adapter implementations pass automated validation checks for interface compliance

<enforcement>
Claude Code MUST NOT skip or defer verification of adapter error handling interfaces and configuration naming conventions. All new adapter implementations MUST be validated against these standardized patterns before approval.
</enforcement>