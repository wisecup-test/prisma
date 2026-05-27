# Enforce Input Validation and Sanitization for External Data: Validation Logic Reject

These rules are ALWAYS ACTIVE for all code that processes external input, user-supplied data, or untrusted sources including user input from web forms and API requests, data from configuration files and environment variables, parameters from third-party APIs and webhooks, file uploads and binary data, and database query results from untrusted sources.

### Rules

- **R-VAL-001** MUST: Validation logic MUST reject invalid input with clear error messages rather than attempting to correct or coerce malformed data.

### Verify

```bash
# Count validation/sanitization patterns in codebase
grep -r "validate\|sanitize\|schema" --include="*.ts" --include="*.js" | wc -l

# Identify unvalidated external input usage
grep -r "req\.body\|req\.query\|req\.params" --include="*.ts" --include="*.js" | grep -v "validate" | head -20

# Run validation-related tests
npm test -- --testPathPattern="validation|sanitize" --passWithNoTests
```

**Accept when:**
- All external input points have explicit validation logic that rejects invalid data
- Validation tests exist covering both valid and invalid input scenarios with at least 80% coverage
- Code review checklist includes verification that new input points have appropriate validation
- Static analysis or linting rules detect unvalidated external input usage

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated security scanning in CI/CD pipeline MUST detect unvalidated input usage. Code review process MUST include security-focused checklist items. Violations are tracked as security issues with mandatory fix timelines based on severity.
</enforcement>