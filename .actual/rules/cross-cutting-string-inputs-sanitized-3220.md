# Enforce Input Validation and Sanitization for External Data: String Inputs Sanitized

These rules are ALWAYS ACTIVE for all code that processes external input, user-supplied data, or untrusted sources across the codebase.

### Rules

- **R-SANITIZE-001** MUST: String inputs MUST be sanitized to prevent injection attacks (SQL injection, XSS, command injection).

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
Claude Code MUST NOT skip or defer verification. Violations are tracked as security issues with mandatory fix timelines. Repeat violations trigger additional security training. Exceptions require security team review and documentation.
</enforcement>