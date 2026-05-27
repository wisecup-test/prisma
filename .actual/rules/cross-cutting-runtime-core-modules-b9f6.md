# Standardize TypeScript Module Imports for Prisma Client Core Libraries: Runtime Core Modules

These rules are ALWAYS ACTIVE for all TypeScript files in packages/client/src, packages/client/tests, packages/adapter-pg, packages/adapter-neon, packages/adapter-planetscale, and packages/integration-tests directories.

### Rules

- **R-RUNTIME-001** SHOULD: Runtime core modules (compositeProxy, validation, conversion) SHOULD be organized in dedicated subdirectories with clear single-responsibility boundaries.
- **R-RUNTIME-002** MUST: Do not import test utilities or test-specific code into runtime core modules (packages/client/src/runtime).
- **R-RUNTIME-003** MUST: Do not use cross-package relative imports (../../packages/) in production code; use explicit package entry points instead.
- **R-RUNTIME-004** MUST: Database adapter packages (adapter-pg, adapter-neon, adapter-planetscale) MUST NOT import from other adapter packages.
- **R-RUNTIME-005** SHOULD: Test files SHOULD distinguish between testing public APIs (import from package entry point) and testing internal implementations (import from specific module paths).

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
- Each adapter package (adapter-pg, adapter-neon, adapter-planetscale) has clear src/ directory structure with conversion utilities, error handling, and adapter implementation in separate modules

<enforcement>
Claude Code MUST NOT skip or defer verification. All verification commands MUST pass before accepting changes to module imports in the specified scope.
</enforcement>