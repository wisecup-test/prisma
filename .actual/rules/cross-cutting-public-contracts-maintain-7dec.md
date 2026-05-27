# Standardize Public API Configuration and Schema Export Patterns: Public Contracts Maintain

These rules are ALWAYS ACTIVE for all public API modules, client configuration files, adapter implementations, and schema management utilities within the Prisma ecosystem.

### Rules

- **R-PUB-001** MUST: Public API contracts MUST maintain backward compatibility across minor version releases, with breaking changes reserved for major versions.

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
Claude Code MUST NOT skip or defer verification of public API contract compliance. All changes to public-facing interfaces, configuration exports, and adapter implementations MUST be validated against these rules before approval.
</enforcement>