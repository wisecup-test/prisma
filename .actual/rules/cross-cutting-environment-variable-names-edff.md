# Standardize Environment Variable Configuration Sources for Runtime Behavior: Environment Variable Names

These rules are ALWAYS ACTIVE for all runtime configuration and environment management implementations across application runtime, testing infrastructure, and CI/CD pipelines.

### Rules

- **R-ENV-001** SHOULD: Environment variable names SHOULD follow a consistent naming convention with prefixes indicating the component or subsystem (e.g., PRISMA_, DATABASE_, CI_).

### Verify

```bash
# Find all process.env accesses outside test and node_modules directories
grep -r 'process\.env\.' --include='*.ts' --include='*.js' | grep -v '__tests__' | grep -v 'node_modules'

# Locate example environment configuration files
find . -name '*.env.example' -o -name 'config.*.ts' | xargs grep -l 'process.env'

# Run configuration-related tests
npm test -- --grep 'configuration|environment' 2>&1 | grep -E '(passing|failing)'
```

**Accept when:**
- All process.env accesses are found in designated configuration modules or properly validated at usage sites
- Example environment configuration files exist and are kept up to date with actual usage
- Configuration-related tests pass, demonstrating proper validation and error handling for missing or invalid values
- Environment variable names follow consistent prefixing conventions across the codebase
- A central registry or documentation of all environment variables exists with their purpose, type, and required/optional status

<enforcement>
Claude Code MUST NOT skip or defer verification. All process.env accesses must be audited for naming convention compliance. Configuration validation tests must pass before acceptance.
</enforcement>