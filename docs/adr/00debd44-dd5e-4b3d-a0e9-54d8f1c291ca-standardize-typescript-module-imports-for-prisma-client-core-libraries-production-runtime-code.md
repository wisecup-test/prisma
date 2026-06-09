# Standardize TypeScript Module Imports for Prisma Client Core Libraries: Production Runtime Code

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all TypeScript modules within the Prisma client packages, adapters, and integration test suites.

## Context

- The Prisma ecosystem consists of multiple interconnected packages including client runtime, database adapters (PostgreSQL, PlanetScale, Neon), and extensive test suites requiring consistent module organization
- TypeScript module imports across 120 files demonstrate a standardized pattern for organizing core library dependencies, runtime components, and test utilities
- The codebase spans multiple concerns: client generation, database adapters, integration tests, type validation, and runtime proxy implementations requiring clear module boundaries
- E2E tests, functional tests, and integration tests need reliable imports from core libraries to validate client behavior across different TypeScript versions and configurations
- The pattern emerged organically across packages/client, packages/adapter-*, and packages/integration-tests directories indicating a project-wide architectural convention

## Problem Statement

Without standardized module import patterns for core Prisma libraries, the codebase risks inconsistent dependency management, circular dependencies, unclear module boundaries, and difficulty maintaining compatibility across multiple database adapters and TypeScript versions. The challenge is establishing clear rules for how runtime components, adapters, test utilities, and type definitions should be imported and organized across the monorepo structure.

## Decision

1. MUST_NOT: Production runtime code MUST NOT import from test utilities or test-specific modules

## Policy Block

- MUST_NOT Production runtime code MUST NOT import from test utilities or test-specific modules

In scope:
- All TypeScript files in packages/client/src and packages/client/tests
- All database adapter packages: packages/adapter-pg, packages/adapter-neon, packages/adapter-planetscale
- All integration test suites in packages/integration-tests
- Runtime core modules including compositeProxy, validation, and conversion utilities
- E2E test configurations and step definitions
- Type validation test files (*.test-d.ts)

Out of scope:
- Generated Prisma Client code in node_modules/.prisma
- Third-party dependencies and external libraries
- Build artifacts and compiled JavaScript output
- Documentation and markdown files
- Configuration files (tsconfig.json, package.json) unless they define module resolution

Exceptions:
- EXC-001: Build helper scripts (e.g., helpers/build.ts) need to import from multiple packages for code generation or bundling purposes
- EXC-002: Migration or refactoring scripts temporarily need to violate module boundaries during codebase restructuring

## Rationale

- Pattern detected across 120 files with 90.87% confidence indicates this is an established, stable architectural convention rather than an emerging experiment
- Evidence spans critical components (runtime core, database adapters, test infrastructure) demonstrating the pattern successfully scales across diverse functional domains
- Consistent module organization in adapter packages (adapter-pg, adapter-neon, adapter-planetscale) proves the pattern enables clean separation of database-specific implementations
- The presence of this pattern in both production code (runtime/core) and test infrastructure (e2e, functional, integration) shows it supports the full development lifecycle

## Consequences

Positive:
- Clear module boundaries reduce cognitive load for developers navigating the large monorepo structure
- Isolated adapter packages enable independent versioning and testing of database-specific implementations
- Explicit import paths improve IDE autocomplete, refactoring tools, and static analysis capabilities
- Consistent test import patterns make it easier to write new tests and maintain existing test suites across TypeScript version upgrades

Negative:
- Explicit import paths can be more verbose than barrel exports, requiring longer import statements
- Refactoring module locations requires updating all import statements rather than just barrel export files
- New contributors may need additional onboarding to understand the module organization conventions
- Strict module boundaries may occasionally require duplicating small utility functions across packages

## Alternatives

- Use barrel exports (index.ts files) to re-export all public APIs from each package (rejected)
  Rejected because: Barrel exports can create circular dependency issues in large monorepos and make it harder to track actual dependencies between modules. They also increase bundle sizes by making tree-shaking less effective.
  When valid: May be appropriate for small, stable packages with well-defined public APIs that rarely change
- Allow relative imports (../../) throughout the codebase without enforcing package boundaries (rejected)
  Rejected because: Relative imports across package boundaries violate monorepo isolation principles and make it difficult to extract packages or manage dependencies. The current pattern enforces cleaner architecture.
  When valid: Acceptable within a single package's internal modules, but not across package boundaries
- Implement path aliases (@runtime/*, @adapters/*) via TypeScript configuration (deferred)
  Rejected because: Not rejected, but deferred pending evaluation of tooling support and build complexity. Path aliases could complement the current pattern.
  When valid: Could be adopted if it simplifies imports without compromising module boundary enforcement

## Risks

- Module boundary violations may be introduced gradually as the codebase grows, especially by new contributors unfamiliar with the pattern
  Mitigation: Implement ESLint rules to enforce import patterns and add automated checks in CI to detect cross-package boundary violations
  Owner: Engineering team / DevOps
- Overly strict module boundaries could lead to code duplication when small utilities are needed across multiple packages
  Mitigation: Create a shared utilities package for truly cross-cutting concerns, and document when duplication is acceptable vs. when to extract shared code
  Owner: Architecture team
- TypeScript version upgrades or module resolution changes could break existing import patterns
  Mitigation: Maintain comprehensive E2E tests across multiple TypeScript versions (as evidenced by ts-version/5.8 tests) and test module resolution in CI
  Owner: Engineering team

## Implementation Notes

- Use TypeScript's 'paths' configuration in tsconfig.json to map package names to source directories during development while maintaining explicit imports
- Organize each adapter package with a clear src/ directory structure: conversion utilities, error handling, and adapter implementation in separate modules
- For test files, distinguish between testing public APIs (import from package entry point) and testing internal implementations (import from specific module paths)
- Document the module organization in each package's README.md with examples of correct import patterns for common scenarios
- Consider using tools like dependency-cruiser or eslint-plugin-import to enforce and visualize module boundaries

## Continuation Context


Verify commands:
- grep -r "from ['\"]\.\..*packages/" packages/*/src --include="*.ts" | grep -v test | wc -l | awk '{if ($1 == 0) print "PASS: No cross-package relative imports"; else print "FAIL: Found cross-package relative imports"}'
- find packages/adapter-* -name "*.ts" -exec grep -l "from.*adapter-" {} \; | grep -v "from.*@prisma/adapter" | wc -l | awk '{if ($1 == 0) print "PASS: No cross-adapter dependencies"; else print "FAIL: Found cross-adapter dependencies"}'
- grep -r "from.*test" packages/client/src/runtime --include="*.ts" --exclude="*.test.ts" | wc -l | awk '{if ($1 == 0) print "PASS: No test imports in runtime"; else print "FAIL: Runtime imports test code"}'

Accept when:
- All verification commands pass with zero violations of module boundary rules
- New TypeScript files in adapter packages maintain isolated dependencies with no cross-adapter imports
- Runtime core modules contain no imports from test utilities or test-specific code
- Integration tests successfully import from package entry points and test utilities follow consistent patterns

## Enforcement

- Verified by: Automated CI checks running grep-based verification commands on every pull request
- Verified by: ESLint rules configured to detect and flag module boundary violations during development
- Verified by: Code review checklist items specifically checking import patterns in new files
- Verified by: Periodic architecture reviews examining module dependency graphs
- Violation handling: CI build fails if verification commands detect module boundary violations
- Violation handling: Pull requests with violations are blocked from merging until imports are corrected
- Violation handling: ESLint errors must be resolved before code can be committed (if pre-commit hooks are enabled)
- Violation handling: Existing violations discovered in legacy code should be tracked in technical debt backlog with prioritization
- Exception process: Developer identifies legitimate need for exception and documents rationale in code comments
- Exception process: Exception request submitted to architecture team via GitHub issue with justification and impact analysis
- Exception process: Architecture team reviews within one sprint and either approves with conditions or suggests alternative approach
- Exception process: Approved exceptions are documented in policy_exceptions section and added to ESLint ignore patterns with tracking comments