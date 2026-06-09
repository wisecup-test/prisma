# Standardize Environment Variable Configuration Sources for Runtime Behavior: Configuration Modules Provide

These rules are ALWAYS ACTIVE for all runtime configuration and environment management implementations across application runtime, testing infrastructure, and CI/CD pipelines.

### Rules

- **R-CONFIG-001** SHOULD: Configuration modules SHOULD provide default values for non-critical settings while requiring explicit values for security-sensitive or environment-specific parameters.

### Verify

```bash
# Find all process.env accesses outside configuration modules
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
- Configuration modules provide typed interfaces to the rest of the application
- Schema validation is implemented for configuration structure at startup
- Sensitive configuration values are not logged or exposed in error messages

<enforcement>
Claude Code MUST NOT skip or defer verification. All process.env accesses must be centralized in configuration modules with proper validation and documentation. CI/CD pipeline validation must ensure all required environment variables are documented before deployment.
</enforcement>