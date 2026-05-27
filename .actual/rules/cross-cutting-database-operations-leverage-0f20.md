# Adopt Prisma ORM as Primary Database Access Layer: Database Operations Leverage

These rules are ALWAYS ACTIVE for all database access patterns and operations across the codebase, including application-level queries, mutations, schema migrations, transaction management, type definitions, and integration/unit tests involving database operations.

### Rules

- **R-PRISMA-001** MUST: All database operations MUST leverage Prisma's type-safe query builder to prevent runtime type errors.

### Verify

```bash
# Verify Prisma Client imports are used
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
- TypeScript compilation succeeds without type errors for database operations

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated CI pipeline checks for Prisma Client imports and schema validation are mandatory. Code review must verify new database access code follows Prisma patterns. TypeScript compilation enforces type-safe database operations at build time. Integration test suite validates database operations against actual database instances.
</enforcement>