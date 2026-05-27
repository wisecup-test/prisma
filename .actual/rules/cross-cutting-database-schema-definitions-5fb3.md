# Adopt Prisma ORM as Primary Database Access Layer: Database Schema Definitions

These rules are ALWAYS ACTIVE for all database access patterns, schema definitions, and database interactions across the codebase.

### Rules

- **R-PRISMA-001** MUST: Database schema definitions MUST be declared in Prisma schema files using the Prisma Schema Language.
- **R-PRISMA-002** MUST: All application-level database queries and mutations MUST use Prisma Client imported from '@prisma/client'.
- **R-PRISMA-003** MUST: Database operations MUST use Prisma's type-safe query methods (findMany, create, update, delete, etc.) rather than raw SQL strings, except where documented exceptions apply.
- **R-PRISMA-004** SHOULD: Transaction management for multi-step operations SHOULD be implemented using Prisma's transaction API.
- **R-PRISMA-005** SHOULD: Type definitions for database models and relations SHOULD be generated from the Prisma schema rather than manually maintained.
- **R-PRISMA-006** MAY: Raw SQL may be used only when EXC-001 (performance profiling demonstrates >50% improvement) or EXC-002 (legacy system integration) exceptions are documented and approved.

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
- Any raw SQL usage is documented with exception reference (EXC-001 or EXC-002) and technical justification

<enforcement>
Claude Code MUST NOT skip or defer verification. Schema validation and Prisma Client import verification are mandatory before accepting database access code.
</enforcement>