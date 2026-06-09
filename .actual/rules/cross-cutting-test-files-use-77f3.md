# Adopt Jest as Standard Testing Framework with Structured Test Organization: Test Files Use

These rules are ALWAYS ACTIVE for all test files across all packages in the codebase, including unit tests, integration tests, functional tests, and end-to-end tests.

### Rules

- **R-JEST-001** MUST: All test files MUST use Jest as the testing framework with TypeScript support via ts-jest or equivalent configuration.

### Verify

```bash
# Check for test files using Jest naming conventions
find . -name '*.test.ts' -o -name 'test.ts' -o -name 'tests.ts' | head -20

# Verify Jest dependencies or imports are present
grep -r "from '@jest'" --include='*.ts' --include='*.json' | head -10

# Check for standard Jest test directory structure
find . -type d -name '__tests__' -o -name 'tests' | head -20

# Verify Jest configuration exists
cat package.json | grep -A 5 '"jest"' || cat jest.config.js || cat jest.config.ts

# Verify test execution works
npm test || jest
```

**Accept when:**
- Test files are found in `__tests__/` or `tests/` directories with `.test.ts` or `test.ts` naming conventions
- Jest dependencies or configuration are present in `package.json` or `jest.config` files
- Test directory structure shows organization by test type (`integration/`, `functional/`, `e2e/`, `unit/`)
- `npm test` or `jest` commands successfully execute test suites without manual configuration
- TypeScript support is configured via `ts-jest` or equivalent in Jest configuration

<enforcement>
Claude Code MUST NOT skip or defer verification of Jest usage and test file organization. All test files must conform to the Jest standard and naming conventions defined in this rule.
</enforcement>