# Standardize Public API Configuration and Schema Export Patterns: Public Modules Export

These rules are ALWAYS ACTIVE for all public API modules, client configuration files, adapter implementations, and schema management utilities within the Prisma ecosystem.

### Rules

- **R-PUBMOD-001** MUST: All public API modules MUST export configuration through standardized file patterns (prisma.config.ts for runtime configuration, _schema.ts for schema definitions).
- **R-PUBMOD-002** MUST: All database adapter implementations MUST export standardized error handling and type conversion interfaces.
- **R-PUBMOD-003** MUST: Configuration files MUST follow naming conventions (prisma.config.ts, _schema.ts) across test suites and integration scenarios.
- **R-PUBMOD-004** MUST: Public API contracts MUST maintain backward compatibility as verified by integration test suite.
- **R-PUBMOD-005** MUST: New adapter implementations MUST pass automated validation checks for interface compliance.

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
Claude Code MUST NOT skip or defer verification. CI pipeline MUST fail on detection of non-compliant configuration patterns or missing required interfaces. Pull requests introducing violations MUST be blocked until compliance is achieved or exception is approved.
</enforcement>