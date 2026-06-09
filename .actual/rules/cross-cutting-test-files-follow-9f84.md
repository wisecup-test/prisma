# Adopt Jest as Standard Testing Framework with Structured Test Organization: Test Files Follow

These rules are ALWAYS ACTIVE for all TypeScript test files across all packages, including unit tests, integration tests, functional tests, and end-to-end tests.

### Rules

- **R-JEST-001** MUST: Test files MUST follow naming conventions: unit tests use `.test.ts` suffix, integration/e2e tests use `test.ts` or `tests.ts` filenames.

### Verify

```bash
# Find test files following Jest conventions
find . -name '*.test.ts' -o -name 'test.ts' -o -name 'tests.ts' | head -20

# Check for Jest dependencies or imports
grep -r "from '@jest'" --include='*.ts' --include='*.json' | head -10

# Find test directories organized by type
find . -type d -name '__tests__' -o -name 'tests' | head -20

# Verify Jest configuration exists
cat package.json | grep -A 5 '"jest"' || cat jest.config.js || cat jest.config.ts
```

**Accept when:**
- Test files are found in `__tests__/` or `tests/` directories with `.test.ts` or `test.ts` naming conventions
- Jest dependencies or configuration are present in `package.json` or `jest.config` files
- Test directory structure shows organization by test type (`integration/`, `functional/`, `e2e/`, `unit/`)
- `npm test` or `jest` commands successfully execute test suites without manual configuration

<enforcement>
Claude Code MUST NOT skip or defer verification of test file naming conventions and Jest configuration presence.
</enforcement>