# Standardize TypeScript Module Imports for Prisma Client Core Libraries: Helper Modules Build

These rules are ALWAYS ACTIVE for all TypeScript modules within the Prisma client packages, adapters, and integration test suites.

### Rules

- **R-HELPER-001** MAY: Helper modules for build processes and code generation MAY use relaxed import patterns when operating in development-only contexts.

### Verify

```bash
# Check for cross-package relative imports in source code
grep -r "from ['\"]\.\..*packages/" packages/*/src --include="*.ts" | grep -v test | wc -l | awk '{if ($1 == 0) print "PASS: No cross-package relative imports"; else print "FAIL: Found cross-package relative imports"}'

# Check for cross-adapter dependencies
find packages/adapter-* -name "*.ts" -exec grep -l "from.*adapter-" {} \; | grep -v "from.*@prisma/adapter" | wc -l | awk '{if ($1 == 0) print "PASS: No cross-adapter dependencies"; else print "FAIL: Found cross-adapter dependencies"}'

# Check that runtime core modules don't import test utilities
grep -r "from.*test" packages/client/src/runtime --include="*.ts" --exclude="*.test.ts" | wc -l | awk '{if ($1 == 0) print "PASS: No test imports in runtime"; else print "FAIL: Runtime imports test code"}'
```

**Accept when:**
- All verification commands pass with zero violations of module boundary rules
- New TypeScript files in adapter packages maintain isolated dependencies with no cross-adapter imports
- Runtime core modules contain no imports from test utilities or test-specific code
- Integration tests successfully import from package entry points and test utilities follow consistent patterns
- Build helper scripts (e.g., helpers/build.ts) are documented as exceptions when they legitimately need to import from multiple packages for code generation or bundling purposes

<enforcement>
Claude Code MUST NOT skip or defer verification. All verification commands MUST pass before accepting changes to TypeScript module imports in this scope.
</enforcement>