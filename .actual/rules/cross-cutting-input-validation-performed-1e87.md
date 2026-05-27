# Adopt Prisma ORM as Primary Database Access Layer: Input Validation Performed

These rules are ALWAYS ACTIVE for all database access patterns and input validation workflows across the codebase.

### Rules

- **R-PRISMA-001** SHOULD: Input validation SHOULD be performed using Prisma validators before database operations.

### Verify

```bash
# Verify Prisma Client imports are used for database access
grep -r 'from @prisma/client' --include='*.ts' --include='*.js' | wc -l

# Verify schema.prisma file exists
find . -name 'schema.prisma' -type f

# Verify PrismaClient instantiation
grep -r 'new PrismaClient' --include='*.ts' --include='*.js'

# Validate Prisma schema
npx prisma validate
```

**Accept when:**
- All database access code imports from '@prisma/client' and uses generated PrismaClient
- At least one schema.prisma file exists in the project with valid schema definitions
- Database operations use Prisma's type-safe query methods (findMany, create, update, delete, etc.) rather than raw SQL strings
- Test files demonstrate integration testing patterns for database operations
- Input validation is performed using Prisma validators before database mutations

<enforcement>
Claude Code MUST NOT skip or defer verification. TypeScript compilation, CI pipeline checks for Prisma Client imports and schema validation, code review processes, and integration test suites are mandatory enforcement mechanisms.
</enforcement>