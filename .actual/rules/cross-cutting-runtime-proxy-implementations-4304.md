# Standardize Public API Configuration and Schema Export Patterns: Runtime Proxy Implementations

These rules are ALWAYS ACTIVE for all public API modules, client configuration files, adapter implementations, schema management utilities, and runtime proxy implementations within the Prisma ecosystem.

### Rules

- **R-PROXY-001** MUST: Runtime proxy implementations (e.g., compositeProxy) MUST preserve type safety and provide transparent access to underlying data structures.
- **R-PROXY-002** MUST: All client library configuration files follow standardized naming conventions (prisma.config.ts, _schema.ts).
- **R-PROXY-003** MUST: Database adapter implementations export standardized error handling and type conversion interfaces.
- **R-PROXY-004** MUST: Public API contracts maintain backward compatibility across adapter implementations.
- **R-PROXY-005** SHOULD: New adapter implementations provide clear extension points for provider-specific functionality while maintaining core contract compliance.
- **R-PROXY-006** SHOULD: Configuration structures be modular and support independent testing of adapter components.

### Verify

```bash
# Verify standardized config file naming
grep -r 'prisma.config.ts' packages/ | wc -l

# Check adapter interface exports for error handling and type conversion
find packages/adapter-* -name 'errors.ts' -o -name 'conversion.ts' | xargs grep -l 'export.*Error\|export.*convert'

# Run integration tests for public API patterns
npm run test:integration -- --grep 'configuration|schema|adapter'

# Validate configuration file structure across test suites
find . -name 'prisma.config.ts' -o -name '_schema.ts' | xargs ls -la
```

**Accept when:**
- All database adapter packages export standardized error handling and type conversion interfaces
- Configuration files follow naming conventions (prisma.config.ts, _schema.ts) across test suites and integration scenarios
- Public API contracts maintain backward compatibility as verified by integration test suite
- New adapter implementations pass automated validation checks for interface compliance
- Runtime proxy implementations preserve type safety and provide transparent access to underlying structures
- Integration tests covering public API contract compliance across all adapters pass successfully

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules. All pull requests introducing changes to public API modules, adapter implementations, or configuration files MUST pass the verification commands and acceptance criteria before approval.
</enforcement>