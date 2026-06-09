# Adopt Jest as Standard Testing Framework with Structured Test Organization: Packages Define Custom

These rules are ALWAYS ACTIVE for all TypeScript test files across all packages, including unit tests, integration tests, functional tests, and end-to-end tests.

### Rules

- **R-JEST-001** MAY: Packages MAY define custom Jest configurations to accommodate specific testing requirements while maintaining framework consistency.

### Verify

```bash
# Check for Jest test files using standard naming conventions
find . -name '*.test.ts' -o -name 'test.ts' -o -name 'tests.ts' | head -20

# Verify Jest dependencies or imports are present
grep -r "from '@jest'" --include='*.ts' --include='*.json' | head -10

# Check for standard Jest test directories
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
Claude Code MUST NOT skip or defer verification of Jest test file organization and configuration presence.
</enforcement>