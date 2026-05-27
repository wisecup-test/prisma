# Adopt Prisma ORM as Primary Database Access Layer: Database Queries Include

These rules are ALWAYS ACTIVE for all database access patterns and queries in the codebase. All new database interactions MUST follow the Prisma ORM patterns defined herein.

### Rules

- **R-PRISMA-001** SHOULD: Database queries SHOULD include trace context metadata (e.g., sqlcommenter) for observability in production environments.

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
- Trace context metadata is included in production database queries for observability

<enforcement>
Claude Code MUST NOT skip or defer verification. CI pipeline MUST fail if schema.prisma validation fails or Prisma Client is not generated. Code review MUST block merge if raw SQL is used without documented exception approval. TypeScript compilation errors MUST prevent deployment of type-unsafe database operations.
</enforcement>