# Standardize Environment Variable Configuration Sources for Runtime Behavior: Test Suites Provide

These rules are ALWAYS ACTIVE for all runtime configuration and environment management implementations, test suites, and CI/CD pipeline scripts that require runtime configuration.

### Rules

- **R-ENV-001** MUST: Test suites MUST provide mechanisms to override or mock environment variables without affecting global process state.

### Verify

```bash
# Check for process.env access outside designated configuration modules
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
- Test suites demonstrate safe override of environment variables with proper cleanup in setup/teardown hooks
- No sensitive configuration values are exposed in test output or error messages

<enforcement>
Claude Code MUST NOT skip or defer verification of environment variable configuration patterns. All process.env access must be validated against this rule during code review.
</enforcement>