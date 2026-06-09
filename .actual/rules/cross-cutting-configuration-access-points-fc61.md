# Standardize Environment Variable Configuration Sources for Runtime Behavior: Configuration Access Points

These rules are ALWAYS ACTIVE for all runtime configuration and environment management implementations across application runtime, testing infrastructure, and CI/CD pipelines.

### Rules

- **R-CONFIG-001** MUST: Configuration access points MUST validate the presence and format of required environment variables at initialization time, not at first use.

### Verify

```bash
# Find all process.env accesses outside designated configuration modules
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
- Configuration validation fails fast at application startup with clear error messages when required variables are missing
- Sensitive configuration values are not logged or exposed in error messages

<enforcement>
Clause Code MUST NOT skip or defer verification of environment variable validation at initialization time. All configuration access points must be audited for compliance with R-CONFIG-001 before code review approval.
</enforcement>