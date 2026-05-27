# Standardize Environment Variable Configuration Sources for Runtime Behavior: Production Code Not

These rules are ALWAYS ACTIVE for all runtime configuration and environment management implementations across application code, testing infrastructure, and CI/CD pipelines.

### Rules

- **R-ENV-001** MUST NOT: Production code MUST NOT contain hardcoded configuration values that should vary by environment (database URLs, API keys, feature flags).

### Verify

```bash
# Check for direct process.env access outside designated configuration modules
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
- No hardcoded configuration values for environment-specific settings are present in production code
- All required environment variables are documented with their purpose, type, and required/optional status

<enforcement>
Claude Code MUST NOT skip or defer verification. All process.env accesses must be audited and validated against this rule before approval.
</enforcement>