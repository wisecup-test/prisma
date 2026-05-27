# Enforce Input Validation and Sanitization for External Data: External Input Users

These rules are ALWAYS ACTIVE for all code that processes external input, user-supplied data, or untrusted sources including user input from web forms and CLI arguments, data from configuration files and environment variables, parameters from third-party APIs and webhooks, and file uploads and binary data from external sources.

### Rules

- **R-EX-001** MUST: All external input from users, APIs, files, or third-party sources MUST be validated before processing or storage.

### Verify

```bash
# Count validation/sanitization patterns in codebase
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
Claude Code MUST NOT skip or defer verification. Automated security scanning in CI/CD pipeline MUST detect unvalidated input usage. Code review process MUST include security-focused checklist items. Static analysis tools MUST be configured to flag potential validation gaps. CI pipeline MUST fail if static analysis detects unvalidated external input in new code.
</enforcement>