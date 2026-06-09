# Adopt Jest as Standard Testing Framework with Structured Test Organization: Test Files Executable

These rules are ALWAYS ACTIVE for all test files across all packages in the codebase, including unit tests, integration tests, functional tests, and end-to-end tests.

### Rules

- **R-JEST-001** MUST: All test files MUST be executable via `npm test` or `jest` commands without requiring manual configuration changes.

### Verify

```bash
# Find test files using standard Jest naming conventions
find . -name '*.test.ts' -o -name 'test.ts' -o -name 'tests.ts' | head -20

# Verify Jest dependencies or imports are present
grep -r "from '@jest'" --include='*.ts' --include='*.json' | head -10

# Find test directories organized by type
find . -type d -name '__tests__' -o -name 'tests' | head -20

# Check for Jest configuration
cat package.json | grep -A 5 '"jest"' || cat jest.config.js || cat jest.config.ts

# Verify test execution works
npm test -- --listTests
```

**Accept when:**
- Test files are found in `__tests__/` or `tests/` directories with `.test.ts` or `test.ts` naming conventions
- Jest dependencies or configuration are present in `package.json` or `jest.config` files
- Test directory structure shows organization by test type (`integration/`, `functional/`, `e2e/`, `unit/`)
- `npm test` or `jest` commands successfully execute test suites without manual configuration
- All test files execute without errors or configuration warnings

<enforcement>
Claude Code MUST NOT skip or defer verification of test file executability. All test files discovered in the codebase must be confirmed executable via standard Jest commands before accepting changes.
</enforcement>