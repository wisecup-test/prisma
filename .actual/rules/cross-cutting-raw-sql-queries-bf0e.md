# Adopt Prisma ORM as Primary Database Access Layer: Raw Sql Queries

These rules are ALWAYS ACTIVE for all database access patterns and interactions with primary datastores across the codebase.

### Rules

- **R-PRISMA-001** MUST_NOT: Raw SQL queries MUST NOT bypass Prisma's type system unless absolutely necessary for performance-critical operations.

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
- Raw SQL usage is documented with exception reference (EXC-001 or EXC-002) and performance benchmarks

<enforcement>
Claude Code MUST NOT skip or defer verification. All database access code must be validated against Prisma patterns before approval. TypeScript compilation and CI pipeline checks are mandatory enforcement gates.
</enforcement>