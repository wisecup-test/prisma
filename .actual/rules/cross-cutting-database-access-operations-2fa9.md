# Adopt Prisma ORM as Primary Database Access Layer: Database Access Operations

These rules are ALWAYS ACTIVE for all database access patterns in the codebase. All new database interactions MUST follow the Prisma ORM patterns defined herein.

### Rules

- **R-DB-001** MUST: All database access operations MUST use Prisma Client as the primary ORM interface.
- **R-DB-002** MUST: All application-level database queries and mutations MUST use Prisma Client.
- **R-DB-003** MUST: Schema migrations and database structure definitions MUST be managed through Prisma.
- **R-DB-004** MUST: Transaction management for multi-step operations MUST use Prisma's transaction API.
- **R-DB-005** MUST: Type definitions for database models and relations MUST be generated from Prisma schema.
- **R-DB-006** MUST: Integration and unit tests involving database operations MUST use Prisma Client.
- **R-DB-007** SHOULD: Implement comprehensive test coverage including integration tests, type tests, validators, and error handling for database operations.
- **R-DB-008** SHOULD: Configure connection pooling and query logging in Prisma Client instantiation for production environments.
- **R-DB-009** MAY: Raw SQL may be used only when documented exceptions (EXC-001: >50% performance improvement, EXC-002: legacy system integration) are approved and documented in code comments.

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
- Raw SQL usage (if any) is documented with approved exception references (EXC-001 or EXC-002)

<enforcement>
Claude Code MUST NOT skip or defer verification. CI pipeline MUST fail if schema.prisma validation fails or Prisma Client is not generated. Code review MUST block merge if raw SQL is used without documented exception approval. TypeScript compilation errors MUST prevent deployment of type-unsafe database operations.
</enforcement>