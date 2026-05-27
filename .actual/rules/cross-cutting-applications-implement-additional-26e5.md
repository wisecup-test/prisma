# Enforce Input Validation and Sanitization for External Data: Applications Implement Additional

These rules are ALWAYS ACTIVE for all code that processes external input, user-supplied data, or untrusted sources, including user input from web forms and API requests, data from configuration files and environment variables, parameters from third-party APIs and webhooks, file uploads, and any other data entering the system from outside the application's trusted boundaries.

### Rules

- **R-VAL-001** MUST: Validate all external input at system boundaries before processing, including user-supplied data from web forms, API requests, CLI arguments, configuration files, environment variables, third-party APIs, webhooks, and file uploads.
- **R-VAL-002** MUST: Reject invalid input explicitly with clear error messages rather than attempting automatic correction or sanitization that could mask security issues.
- **R-VAL-003** MUST: Use established validation libraries (e.g., Zod, Joi, class-validator) rather than writing custom validation logic from scratch.
- **R-VAL-004** SHOULD: Define validation schemas close to the input boundary and make them explicit in the code (e.g., as TypeScript types or JSON schemas).
- **R-VAL-005** SHOULD: Implement validation as middleware or decorators for API endpoints to ensure consistent application across all routes.
- **R-VAL-006** SHOULD: Log validation failures with sufficient detail for security monitoring while avoiding logging of sensitive user data.
- **R-VAL-007** SHOULD: Create reusable validation utilities for common patterns (email addresses, URLs, file types) to ensure consistency across the codebase.
- **R-VAL-008** MAY: Applications MAY implement additional context-specific validation beyond the baseline requirements.

### Verify

```bash
# Count validation and sanitization patterns in codebase
grep -r "validate\|sanitize\|schema" --include="*.ts" --include="*.js" | wc -l

# Identify unvalidated external input usage
grep -r "req\.body\|req\.query\|req\.params" --include="*.ts" --include="*.js" | grep -v "validate" | head -20

# Run validation-specific tests
npm test -- --testPathPattern="validation|sanitize" --passWithNoTests
```

**Accept when:**
- All external input points have explicit validation logic that rejects invalid data
- Validation tests exist covering both valid and invalid input scenarios with at least 80% coverage
- Code review checklist includes verification that new input points have appropriate validation
- Static analysis or linting rules detect unvalidated external input usage
- Validation schemas are defined close to input boundaries and are explicit in the code
- Validation is implemented consistently across all API endpoints and input handlers

<enforcement>
Claude Code MUST NOT skip or defer verification. All external input must be validated before processing. Violations are security-critical and must be remediated immediately.
</enforcement>