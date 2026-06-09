# Adopt Promise-Based Async/Await Concurrency Model: Asynchronous Functions Declared

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript source files in packages/, all test files using async operations, all build and automation scripts requiring I/O operations, database adapter implementations, HTTP request handlers and middleware, and file system operations.

### Rules

- **R-ASYNC-001** MUST: All asynchronous functions MUST be declared with the async keyword and return Promises.

### Verify

```bash
# Count .then() chains (should be minimal, mostly in legacy code)
grep -r '\.then(' --include='*.ts' --include='*.js' packages/ | grep -v node_modules | wc -l

# Run ESLint with no-floating-promises rule
npx eslint --rule '@typescript-eslint/no-floating-promises: error' packages/

# Count async function declarations (should be high)
grep -r 'async.*function\|function.*async' --include='*.ts' packages/ | wc -l
```

**Accept when:**
- ESLint reports zero violations of no-floating-promises rule across the codebase
- Code review checklist includes verification that parallel async operations use Promise.all()
- At least 90% of async operations use async/await syntax rather than .then() chains (measured by grep ratio)
- All new pull requests pass automated checks for proper async/await usage

<enforcement>
Claude Code MUST NOT skip or defer verification. ESLint pre-commit hooks and CI pipeline checks are mandatory blockers for violations of @typescript-eslint/no-floating-promises and @typescript-eslint/require-await rules.
</enforcement>