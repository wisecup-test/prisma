# Standardize Environment Variable Configuration Sources for Runtime Behavior: Systems Support Multiple

These rules are ALWAYS ACTIVE for all runtime configuration and environment management implementations across application runtime, testing infrastructure, and CI/CD pipelines.

### Rules

- **R-ENV-001** MAY: Systems MAY support multiple configuration sources with defined precedence (environment variables, config files, defaults) as long as environment variables take highest precedence.

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
- Environment variables are documented in a central location with purpose, type, required/optional status, and valid values
- Configuration validation occurs at application startup with clear error messages for missing or invalid values

<enforcement>
Clause Code MUST NOT skip or defer verification. All process.env accesses must be validated, documented, and tested. Configuration modules must encapsulate environment variable access with typed interfaces. Violations result in CI build failures and code review blocks.
</enforcement>