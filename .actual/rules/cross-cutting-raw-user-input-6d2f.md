# Enforce Input Validation and Sanitization for External Data: Raw User Input

These rules are ALWAYS ACTIVE for all code that processes external input, user-supplied data, or untrusted sources including web forms, API requests, CLI arguments, configuration files, environment variables, third-party APIs, webhooks, file uploads, and database results from untrusted sources.

### Rules

- **R-INPUT-001** MUST NOT: Raw user input MUST NOT be directly interpolated into queries, commands, or templates without sanitization.

### Verify

```bash
# Count validation and sanitization patterns in codebase
grep -r "validate\|sanitize\|schema" --include="*.ts" --include="*.js" | wc -l

# Identify unvalidated external input usage patterns
grep -r "req\.body\|req\.query\|req\.params" --include="*.ts" --include="*.js" | grep -v "validate" | head -20

# Run validation and sanitization tests
npm test -- --testPathPattern="validation|sanitize" --passWithNoTests
```

**Accept when:**
- All external input points have explicit validation logic that rejects invalid data
- Validation tests exist covering both valid and invalid input scenarios with at least 80% coverage
- Code review checklist includes verification that new input points have appropriate validation
- Static analysis or linting rules detect unvalidated external input usage
- Established validation libraries (e.g., Zod, Joi, class-validator) are used rather than custom validation logic
- Validation schemas are defined close to the input boundary and made explicit in code
- Validation is implemented as middleware or decorators for API endpoints
- Validation failures are logged with sufficient detail for security monitoring

<enforcement>
Claude Code MUST NOT skip or defer verification. Static analysis tools MUST be configured to flag potential validation gaps. CI pipeline MUST fail if unvalidated external input is detected in new code. Security team MUST review violations and prioritize remediation based on severity.
</enforcement>