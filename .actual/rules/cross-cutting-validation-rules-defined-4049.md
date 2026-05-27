# Enforce Input Validation and Sanitization for External Data: Validation Rules Defined

These rules are ALWAYS ACTIVE for all code that processes external input, user-supplied data, or untrusted sources including web forms, API requests, CLI arguments, configuration files, environment variables, third-party APIs, webhooks, file uploads, and binary data from external sources.

### Rules

- **R-VAL-001** SHOULD: Validation rules SHOULD be defined using schema validation libraries or type systems rather than ad-hoc checks.

### Verify

```bash
# Count validation/sanitization/schema usage across codebase
grep -r "validate\|sanitize\|schema" --include="*.ts" --include="*.js" | wc -l

# Identify unvalidated external input usage patterns
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
Claude Code MUST NOT skip or defer verification. Automated security scanning in CI/CD pipeline detects unvalidated input usage. Code review process includes security-focused checklist items. Violations are tracked as security issues with mandatory fix timelines. Repeat violations trigger additional security training.
</enforcement>