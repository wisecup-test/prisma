# Adopt Prisma ORM as Primary Database Access Layer: Teams Use External

These rules are ALWAYS ACTIVE for all database access patterns and application-level database interactions across the codebase.

### Rules

- **R-PRISMA-001** MUST: All new database interactions use Prisma ORM patterns and import from `@prisma/client`.
- **R-PRISMA-002** MUST: Database schema definitions are maintained in `schema.prisma` files and validated before deployment.
- **R-PRISMA-003** MUST: Database operations use Prisma's type-safe query methods (findMany, create, update, delete, etc.) rather than raw SQL strings.
- **R-PRISMA-004** MUST: Schema changes are applied through Prisma Migrate (`npx prisma migrate dev` for development, `npx prisma migrate deploy` for production).
- **R-PRISMA-005** SHOULD: Comprehensive test coverage includes integration tests for database operations, type tests for model definitions, and error handling tests for constraint violations.
- **R-PRISMA-006** SHOULD: Connection pooling and query logging are configured in Prisma Client instantiation for production environments.
- **R-PRISMA-007** MAY: Teams MAY use external tables integration when interfacing with legacy database schemas or shared databases.
- **R-PRISMA-EXC-001** MAY: Raw SQL may be used when performance profiling demonstrates >50% performance improvement for a specific query pattern (requires documented exception and technical lead approval).
- **R-PRISMA-EXC-002** MAY: Raw SQL may be used when interfacing with legacy systems requiring database-specific features not supported by Prisma (requires documented exception and technical lead approval).

### Verify

```bash
# Count Prisma Client imports
grep -r 'from @prisma/client' --include='*.ts' --include='*.js' | wc -l

# Find schema.prisma files
find . -name 'schema.prisma' -type f

# Find PrismaClient instantiations
grep -r 'new PrismaClient' --include='*.ts' --include='*.js'

# Validate Prisma schema
npx prisma validate
```

**Accept when:**
- All database access code imports from `@prisma/client` and uses generated PrismaClient
- At least one `schema.prisma` file exists in the project with valid schema definitions
- Database operations use Prisma's type-safe query methods (findMany, create, update, delete, etc.) rather than raw SQL strings
- Test files demonstrate integration testing patterns for database operations
- TypeScript compilation succeeds without type-safety errors for database operations
- Schema validation passes via `npx prisma validate`

<enforcement>
Claude Code MUST NOT skip or defer verification. All database access patterns MUST conform to Prisma ORM standards. Raw SQL usage requires documented exceptions with performance benchmarks or technical justification, reviewed and approved by technical lead, and logged in architecture decision records.
</enforcement>