# Standardize Public API Configuration and Schema Export Patterns: Type Conversion Utilities

These rules are ALWAYS ACTIVE for all public API modules, client configuration files, adapter implementations, and schema management utilities within the Prisma ecosystem.

### Rules

- **R-API-001** MUST: Type conversion utilities in adapters MUST maintain bidirectional transformation guarantees between database-native types and Prisma's type system.

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
- Type conversion utilities demonstrate bidirectional transformation guarantees in test coverage

<enforcement>
Claude Code MUST NOT skip or defer verification of type conversion utilities and adapter interface compliance. All new adapter implementations and configuration files must pass automated validation checks before acceptance.
</enforcement>