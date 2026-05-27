# Standardize TypeScript Module Imports for Prisma Client Core Libraries: Production Runtime Code

These rules are ALWAYS ACTIVE for all TypeScript modules within the Prisma client packages, adapters, and integration test suites.

### Rules

- **R-PRISMA-001** MUST NOT: Production runtime code MUST NOT import from test utilities or test-specific modules.

### Verify

```bash
# Check for cross-package relative imports in production code
grep -r "from ['\"]\.\..*packages/" packages/*/src --include="*.ts" | grep -v test | wc -l | awk '{if ($1 == 0) print "PASS: No cross-package relative imports"; else print "FAIL: Found cross-package relative imports"}'

# Check for cross-adapter dependencies
find packages/adapter-* -name "*.ts" -exec grep -l "from.*adapter-" {} \; | grep -v "from.*@prisma/adapter" | wc -l | awk '{if ($1 == 0) print "PASS: No cross-adapter dependencies"; else print "FAIL: Found cross-adapter dependencies"}'

# Check for test imports in runtime code
grep -r "from.*test" packages/client/src/runtime --include="*.ts" --exclude="*.test.ts" | wc -l | awk '{if ($1 == 0) print "PASS: No test imports in runtime"; else print "FAIL: Runtime imports test code"}'
```

**Accept when:**
- All verification commands pass with zero violations of module boundary rules
- New TypeScript files in adapter packages maintain isolated dependencies with no cross-adapter imports
- Runtime core modules contain no imports from test utilities or test-specific code
- Integration tests successfully import from package entry points and test utilities follow consistent patterns

<enforcement>
Claude Code MUST NOT skip or defer verification. All three bash commands must execute successfully before accepting changes to production runtime code.
</enforcement>