# Adopt Prisma ORM as Primary Database Access Layer: Database Operations Leverage

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all database access patterns in the codebase. All new database interactions MUST follow the Prisma ORM patterns defined herein.

## Context

- The codebase demonstrates extensive use of Prisma Client across 22+ files with 92.19% confidence, indicating a standardized approach to database access
- Evidence shows comprehensive test coverage including integration tests, type tests, validators, transactions, and error handling for database operations
- Multiple database providers are supported (SQLServer, SQLite, PostgreSQL) through Prisma's unified interface, as evidenced by referential action tests and external table integration
- The pattern includes sophisticated features like SQL commenter trace context, schema validation, and unchecked scalar validation, indicating mature ORM usage
- Test files demonstrate both happy path scenarios and error handling for foreign key constraints, referential actions, and schema validation

## Problem Statement

Teams need a consistent, type-safe, and maintainable approach to interact with primary datastores across multiple database providers while ensuring data integrity, supporting complex queries, and maintaining developer productivity through strong TypeScript integration and comprehensive validation.

## Decision

1. MUST: All database operations MUST leverage Prisma's type-safe query builder to prevent runtime type errors

## Policy Block

- MUST All database operations MUST leverage Prisma's type-safe query builder to prevent runtime type errors

In scope:
- All application-level database queries and mutations
- Schema migrations and database structure definitions
- Transaction management for multi-step operations
- Type definitions for database models and relations
- Integration and unit tests involving database operations

Out of scope:
- Database administration scripts run outside application context
- Data warehouse ETL processes using specialized tools
- Performance-critical batch operations where raw SQL is demonstrably faster
- Third-party tools that require direct database connections

Exceptions:
- EXC-001: Performance profiling demonstrates that raw SQL provides >50% performance improvement for a specific query pattern
- EXC-002: Interfacing with legacy systems that require database-specific features not supported by Prisma

## Rationale

- Pattern detected across 22 files with 92.19% confidence indicates strong organizational consensus and proven effectiveness in production environments
- Prisma provides type-safe database access that catches errors at compile-time rather than runtime, reducing production incidents and improving developer experience
- Multi-database support (SQLServer, SQLite, PostgreSQL) through a unified API reduces vendor lock-in and simplifies database provider migrations
- Comprehensive test coverage in evidence files demonstrates that Prisma enables robust testing practices including integration tests, type validation, and error scenario testing

## Consequences

Positive:
- Type safety eliminates entire classes of runtime database errors through compile-time validation
- Developer productivity increases through auto-generated TypeScript types and intelligent IDE autocomplete
- Database migrations become version-controlled and reproducible through Prisma Migrate
- Consistent patterns across the codebase reduce cognitive load and onboarding time for new developers
- Built-in connection pooling and query optimization improve application performance

Negative:
- Additional abstraction layer may introduce slight performance overhead compared to hand-optimized raw SQL
- Team must learn Prisma Schema Language and query API, requiring initial training investment
- Complex database-specific features may require workarounds or raw SQL escape hatches
- Schema changes require running migrations which adds steps to deployment process
- Dependency on Prisma's release cycle for bug fixes and new database feature support

## Alternatives

- Use raw SQL with a query builder like Knex.js (rejected)
  Rejected because: Lacks compile-time type safety and requires manual type definitions, increasing maintenance burden and runtime error risk
  When valid: May be reconsidered for performance-critical batch operations after benchmarking
- Use TypeORM as the primary ORM (rejected)
  Rejected because: TypeORM's decorator-based approach is less type-safe than Prisma's generated client, and the existing codebase shows strong Prisma adoption
  When valid: Could be evaluated if migrating to a different architectural pattern (e.g., NestJS with heavy decorator usage)
- Use database-specific native drivers with manual type definitions (rejected)
  Rejected because: Eliminates multi-database portability, increases code duplication across providers, and loses automatic migration management
  When valid: Acceptable for database administration scripts outside application runtime

## Risks

- Prisma may not support edge-case database features required for specific use cases, forcing raw SQL workarounds
  Mitigation: Establish clear exception process (EXC-001, EXC-002) and maintain documentation of workarounds. Monitor Prisma roadmap for feature additions.
  Owner: Database Architecture Team
- Performance degradation in high-throughput scenarios due to ORM overhead
  Mitigation: Implement performance monitoring for database queries. Establish benchmarking process before allowing raw SQL exceptions. Use Prisma's query optimization features.
  Owner: Performance Engineering Team
- Breaking changes in Prisma major version upgrades could require significant refactoring
  Mitigation: Pin Prisma versions in production. Test upgrades in staging environments. Maintain comprehensive test suite (as evidenced by 22+ test files) to catch regressions.
  Owner: Engineering Team

## Implementation Notes

- Initialize Prisma in new projects using 'npx prisma init' and define schema in schema.prisma file
- Generate Prisma Client after schema changes using 'npx prisma generate' and include in build pipeline
- Use Prisma Migrate for schema changes: 'npx prisma migrate dev' for development, 'npx prisma migrate deploy' for production
- Implement comprehensive test coverage following patterns in evidence files: integration tests for database operations, type tests for model definitions, and error handling tests for constraint violations
- Configure connection pooling and query logging in Prisma Client instantiation for production environments
- Use Prisma Studio ('npx prisma studio') for database exploration during development

## Continuation Context


Verify commands:
- grep -r 'from @prisma/client' --include='*.ts' --include='*.js' | wc -l
- find . -name 'schema.prisma' -type f
- grep -r 'new PrismaClient' --include='*.ts' --include='*.js'
- npx prisma validate

Accept when:
- All database access code imports from '@prisma/client' and uses generated PrismaClient
- At least one schema.prisma file exists in the project with valid schema definitions
- Database operations use Prisma's type-safe query methods (findMany, create, update, delete, etc.) rather than raw SQL strings
- Test files demonstrate integration testing patterns for database operations as shown in evidence files

## Enforcement

- Verified by: Automated CI pipeline checks for Prisma Client imports and schema validation
- Verified by: Code review process verifies new database access code follows Prisma patterns
- Verified by: TypeScript compilation enforces type-safe database operations at build time
- Verified by: Integration test suite validates database operations against actual database instances
- Violation handling: CI pipeline fails if schema.prisma validation fails or Prisma Client is not generated
- Violation handling: Code review blocks merge if raw SQL is used without documented exception approval
- Violation handling: TypeScript compilation errors prevent deployment of type-unsafe database operations
- Violation handling: Architecture review board reviews quarterly metrics on exception usage and raw SQL patterns
- Exception process: Developer documents performance benchmark or technical limitation requiring exception
- Exception process: Technical lead reviews and approves exception request with justification
- Exception process: Exception is documented in code comments with reference to EXC-001 or EXC-002
- Exception process: Exception is logged in architecture decision log for quarterly review