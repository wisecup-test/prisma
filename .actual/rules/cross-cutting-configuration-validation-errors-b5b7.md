# Standardize Environment Variable Configuration Sources for Runtime Behavior: Configuration Validation Errors

These rules are ALWAYS ACTIVE for all runtime configuration and environment management implementations across application runtime, testing infrastructure, and CI/CD pipelines.

### Rules

- **R-CONFIG-001** SHOULD: Configuration validation errors SHOULD provide clear, actionable error messages indicating which variables are missing or invalid and what values are expected.

### Verify

```bash
# Check for direct process.env access outside designated configuration modules
grep -r 'process\.env\.' --include='*.ts' --include='*.js' | grep -v '__tests__' | grep -v 'node_modules'

# Verify example environment configuration files exist and are current
find . -name '*.env.example' -o -name 'config.*.ts' | xargs grep -l 'process.env'

# Run configuration-related tests
npm test -- --grep 'configuration|environment' 2>&1 | grep -E '(passing|failing)'
```

**Accept when:**
- All process.env accesses are found in designated configuration modules or properly validated at usage sites
- Example environment configuration files exist and are kept up to date with actual usage
- Configuration-related tests pass, demonstrating proper validation and error handling for missing or invalid values
- Configuration validation errors provide clear messages indicating which variables are missing or invalid
- All required environment variables are documented with their purpose, type, required/optional status, and valid values

<enforcement>
Claude Code MUST NOT skip or defer verification. All configuration validation errors MUST include clear, actionable messages. Violations result in CI build failures for undocumented environment variable usage and code review blocking for hardcoded configuration values.
</enforcement>