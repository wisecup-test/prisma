# Standardize Public API Configuration and Schema Export Patterns: Adapter Implementations Extend

These rules are ALWAYS ACTIVE for all client library configuration files, schema definition exports, database adapter implementations, error handling and type conversion utilities, engine command interfaces, runtime proxy implementations, and integration test configurations within the Prisma ecosystem.

### Rules

- **R-API-001** MAY: Adapter implementations MAY extend base interfaces with provider-specific functionality, provided core contracts remain satisfied.

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
Claude Code MUST NOT skip or defer verification of adapter interface compliance and configuration naming conventions. Violations must be caught during CI pipeline execution and pull request review.
</enforcement>