# Standardize TypeScript Module Imports for Prisma Client Core Libraries: Test Files Import

These rules are ALWAYS ACTIVE for all TypeScript files within the Prisma client packages, adapters, and integration test suites, specifically governing how test files import from core libraries and published package entry points.

### Rules

- **R-PRISMA-TEST-001** MUST: Test files MUST import from published package entry points (e.g., '@prisma/client') rather than internal implementation paths when testing public APIs.

### Verify

```bash
# Check for cross-package relative imports in non-test source files
grep -r "from ['\"]\.\..*packages/" packages/*/src --include="*.ts" | grep -v test | wc -l | awk '{if ($1 == 0) print "PASS: No cross-package relative imports"; else print "FAIL: Found cross-package relative imports"}'

# Check for cross-adapter dependencies
find packages/adapter-* -name "*.ts" -exec grep -l "from.*adapter-" {} \; | grep -v "from.*@prisma/adapter" | wc -l | awk '{if ($1 == 0) print "PASS: No cross-adapter dependencies"; else print "FAIL: Found cross-adapter dependencies"}'

# Check that runtime core modules do not import test code
grep -r "from.*test" packages/client/src/runtime --include="*.ts" --exclude="*.test.ts" | wc -l | awk '{if ($1 == 0) print "PASS: No test imports in runtime"; else print "FAIL: Runtime imports test code"}'
```

**Accept when:**
- All verification commands pass with zero violations of module boundary rules
- New TypeScript files in adapter packages maintain isolated dependencies with no cross-adapter imports
- Runtime core modules contain no imports from test utilities or test-specific code
- Integration tests successfully import from package entry points and test utilities follow consistent patterns
- Test files in packages/client/tests and packages/integration-tests import from '@prisma/client' and other published entry points rather than internal paths

<enforcement>
Claude Code MUST NOT skip or defer verification. All three bash verification commands MUST pass before accepting changes to test file imports. Module boundary violations MUST be corrected before code can be merged.
</enforcement>