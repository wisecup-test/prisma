# Adopt Promise-Based Async/Await Concurrency Model: Top Level Await

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript source files in packages/, all test files using async operations, all build and automation scripts requiring I/O operations, database adapter implementations, HTTP request handlers and middleware, and file system operations.

### Rules

- **R-ASYNC-001** MAY: Top-level await MAY be used in module contexts where the runtime supports it (ES modules, Node.js 14.8+).
- **R-ASYNC-002** MUST: All asynchronous operations must follow the Promise-based async/await concurrency model.
- **R-ASYNC-003** MUST: Enable ESLint rules @typescript-eslint/no-floating-promises and @typescript-eslint/require-await to catch common mistakes.
- **R-ASYNC-004** SHOULD: Use Promise.all() for independent parallel operations and Promise.allSettled() when you need all results regardless of failures.
- **R-ASYNC-005** SHOULD: Wrap third-party callback-based APIs using util.promisify() in Node.js or manual Promise constructors.
- **R-ASYNC-006** SHOULD: Document async functions with @throws JSDoc tags to communicate error conditions to callers.
- **R-ASYNC-007** SHOULD: Consider using Promise.race() for timeout patterns and Promise.any() for fallback strategies.
- **R-ASYNC-008** MAY: EXC-001 exception applies when interfacing with third-party libraries that only provide callback-based APIs.
- **R-ASYNC-009** MAY: EXC-002 exception applies for performance-critical hot paths where Promise overhead is measured and significant.

### Verify

```bash
# Count .then() chains in TypeScript/JavaScript files
grep -r '\.then(' --include='*.ts' --include='*.js' packages/ | grep -v node_modules | wc -l

# Run ESLint with no-floating-promises rule
npx eslint --rule '@typescript-eslint/no-floating-promises: error' packages/

# Count async function declarations
grep -r 'async.*function\|function.*async' --include='*.ts' packages/ | wc -l
```

**Accept when:**
- ESLint reports zero violations of no-floating-promises rule across the codebase
- Code review checklist includes verification that parallel async operations use Promise.all()
- At least 90% of async operations use async/await syntax rather than .then() chains (measured by grep ratio)
- All new pull requests pass automated checks for proper async/await usage
- TypeScript compiler strict mode is enabled and passes without async-related errors

<enforcement>
Claude Code MUST NOT skip or defer verification. Pre-commit hooks MUST block commits with floating promises or improper async usage. CI pipeline MUST run ESLint with async/await rules as blocking checks. Code reviewers MUST verify async/await patterns match this rule set.
</enforcement>