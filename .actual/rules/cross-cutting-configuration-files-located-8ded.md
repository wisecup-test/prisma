# Standardize Public API Configuration and Schema Export Patterns: Configuration Files Located

These rules are ALWAYS ACTIVE for all public API modules, client configuration files, adapter implementations, and schema management utilities within the Prisma ecosystem.

### Rules

- **R-CONFIG-001** SHOULD: Configuration files SHOULD be co-located with their consuming modules in test and integration scenarios to improve discoverability.

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
Verification via CI checks validating configuration file naming conventions and structure is mandatory. Integration test suite coverage of public API contract compliance across all adapters is required. Code review checklist must verify standardized patterns in new adapter implementations. Static analysis tools must check for required interface implementations and export patterns. Claude Code MUST NOT skip or defer verification.
</enforcement>