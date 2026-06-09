# Adopt Prisma ORM as Primary Database Access Layer: Referential Integrity Constraints

These rules are ALWAYS ACTIVE for all database access patterns and schema definitions in the codebase.

### Rules

- **R-PRISMA-001** MUST: Referential integrity constraints and foreign key relationships MUST be defined in the Prisma schema with explicit onDelete and onUpdate actions.

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
- Foreign key relationships in schema.prisma include explicit onDelete and onUpdate actions

<enforcement>
Claude Code MUST NOT skip or defer verification. Schema validation and Prisma Client import verification are mandatory before accepting database access code.
</enforcement>