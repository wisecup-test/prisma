# Adopt Promise-Based Async/Await Concurrency Model: Code Not Use

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript source files in packages/, all test files using async operations, all build and automation scripts requiring I/O operations, database adapter implementations, HTTP request handlers and middleware, and file system operations.

### Rules

- **R-ASYNC-001** MUST_NOT: Code MUST NOT use callback-based asynchronous patterns for new implementations.

### Verify

```bash
# Count .then() chains in TypeScript/JavaScript files
grep -r '\.then(' --include='*.ts' --include='*.js' packages/ | grep -v node_modules | wc -l

# Run ESLint with no-floating-promises rule
npx eslint --rule '@typescript-eslint/no-floating-promises: error' packages/

# Count async/await function declarations
grep -r 'async.*function\|function.*async' --include='*.ts' packages/ | wc -l
```

**Accept when:**
- ESLint reports zero violations of no-floating-promises rule across the codebase
- Code review checklist includes verification that parallel async operations use Promise.all()
- At least 90% of async operations use async/await syntax rather than .then() chains (measured by grep ratio)
- All new pull requests pass automated checks for proper async/await usage

<enforcement>
Claude Code MUST NOT skip or defer verification. ESLint pre-commit hooks and CI pipeline checks are mandatory. Code reviewers must verify async/await patterns in all pull requests. Violations block commits and CI builds.
</enforcement>