# Standardize Environment Variable Configuration Sources for Runtime Behavior: Runtime Configuration Values

These rules are ALWAYS ACTIVE for all runtime configuration and environment management implementations across application runtime, testing infrastructure, and CI/CD pipelines.

### Rules

- **R-ENV-001** MUST: All runtime configuration values MUST be sourced from environment variables accessed through process.env or equivalent platform-specific mechanisms.

### Verify

```bash
# Find all process.env accesses in production code
grep -r 'process\.env\.' --include='*.ts' --include='*.js' | grep -v '__tests__' | grep -v 'node_modules'

# Verify example environment configuration files exist
find . -name '*.env.example' -o -name 'config.*.ts' | xargs grep -l 'process.env'

# Run configuration-related tests
npm test -- --grep 'configuration|environment' 2>&1 | grep -E '(passing|failing)'
```

**Accept when:**
- All process.env accesses are found in designated configuration modules or properly validated at usage sites
- Example environment configuration files exist and are kept up to date with actual usage
- Configuration-related tests pass, demonstrating proper validation and error handling for missing or invalid values
- No hardcoded configuration values exist that should be environment-based
- All required environment variables are documented with purpose, type, and valid values

<enforcement>
Claude Code MUST NOT skip or defer verification. All process.env accesses must be validated against this rule during code review. CI/CD pipeline validation must ensure all required environment variables are documented. Configuration validation tests must pass before acceptance.
</enforcement>