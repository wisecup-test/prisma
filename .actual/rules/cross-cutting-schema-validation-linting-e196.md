# Standardize Public API Configuration and Schema Export Patterns: Schema Validation Linting

These rules are ALWAYS ACTIVE for all public API modules, client configuration files, adapter implementations, and schema management utilities within the Prisma ecosystem.

### Rules

- **R-SCHEMA-001** SHOULD: Schema validation and linting commands SHOULD expose programmatic interfaces alongside CLI interfaces to support tooling integration.

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
Claude Code MUST NOT skip or defer verification of schema validation linting patterns. Automated CI checks MUST validate configuration file naming conventions and structure. Integration test suite MUST cover public API contract compliance across all adapters. Code review MUST verify standardized patterns in new adapter implementations. Static analysis tools MUST check for required interface implementations and export patterns. Pull requests introducing violations MUST be blocked until compliance is achieved or exception is approved.
</enforcement>