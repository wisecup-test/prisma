# Enforce Input Validation and Sanitization for External Data: Input Validation Logic

These rules are ALWAYS ACTIVE for all code that processes external input, user-supplied data, or untrusted sources, including user input from web forms and API requests, data from configuration files and environment variables, parameters from third-party APIs and webhooks, database query results from untrusted sources, and file uploads and binary data from external sources.

### Rules

- **R-INPUT-001** SHOULD: Input validation logic SHOULD be covered by unit tests that verify both valid and invalid input scenarios.

### Verify

```bash
# Count validation and sanitization patterns in codebase
grep -r "validate\|sanitize\|schema" --include="*.ts" --include="*.js" | wc -l

# Identify unvalidated external input usage
grep -r "req\.body\|req\.query\|req\.params" --include="*.ts" --include="*.js" | grep -v "validate" | head -20

# Run validation and sanitization tests
npm test -- --testPathPattern="validation|sanitize" --passWithNoTests
```

**Accept when:**
- All external input points have explicit validation logic that rejects invalid data
- Validation tests exist covering both valid and invalid input scenarios with at least 80% coverage
- Code review checklist includes verification that new input points have appropriate validation
- Static analysis or linting rules detect unvalidated external input usage

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated security scanning in CI/CD pipeline detects unvalidated input usage. Code review process includes security-focused checklist items. Violations are tracked as security issues with mandatory fix timelines based on severity.
</enforcement>