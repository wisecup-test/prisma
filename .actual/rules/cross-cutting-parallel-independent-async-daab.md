# Adopt Promise-Based Async/Await Concurrency Model: Parallel Independent Async

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript source files in packages/, all test files using async operations, all build and automation scripts requiring I/O operations, database adapter implementations, HTTP request handlers and middleware, and file system operations.

### Rules

- **R-ASYNC-001** SHOULD: Parallel independent async operations SHOULD use Promise.all() or Promise.allSettled() for concurrent execution.

### Verify

```bash
# Count .then() chains (should be minimal)
grep -r '\.then(' --include='*.ts' --include='*.js' packages/ | grep -v node_modules | wc -l

# Check for floating promises violations
npx eslint --rule '@typescript-eslint/no-floating-promises: error' packages/

# Count async/await usage
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