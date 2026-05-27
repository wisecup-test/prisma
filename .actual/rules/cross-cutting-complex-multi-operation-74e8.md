# Adopt Prisma ORM as Primary Database Access Layer: Complex Multi Operation

These rules are ALWAYS ACTIVE for all database access patterns and multi-operation workflows in the codebase.

### Rules

- **R-PRISMA-001** SHOULD: Complex multi-operation workflows SHOULD use Prisma transactions ($transaction) to ensure atomicity.

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
- Multi-operation workflows use Prisma $transaction for atomicity

<enforcement>
Claude Code MUST NOT skip or defer verification. TypeScript compilation and CI pipeline checks enforce Prisma Client imports and schema validation. Code review blocks merge if raw SQL is used without documented exception approval.
</enforcement>