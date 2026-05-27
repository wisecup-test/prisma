# Adopt Jest as Standard Testing Framework with Structured Test Organization: Test Files Executable

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all test file creation and test suite organization across the codebase.

## Context

- The codebase contains multiple packages (client, adapters) requiring consistent testing approaches across integration, unit, functional, and e2e test types
- Test files are distributed across 27+ locations with consistent naming patterns (__tests__, tests/, .test.ts, test.ts) indicating an established testing convention
- The project uses TypeScript throughout, requiring a testing framework with native TypeScript support and type-safe assertions
- Multiple test categories exist (integration, functional, e2e, unit) requiring a flexible framework that supports different testing strategies
- Evidence shows consistent test file organization patterns with separation by test type (integration/, functional/, e2e/) and feature areas

## Problem Statement

Without a standardized testing framework and organizational structure, teams may adopt inconsistent testing approaches leading to fragmented test suites, incompatible tooling, duplicated test infrastructure, and difficulty maintaining test quality across packages. A unified testing strategy is needed to ensure consistent test execution, reporting, and developer experience.

## Decision

1. MUST: All test files MUST be executable via npm test or jest commands without requiring manual configuration changes

## Policy Block

- MUST All test files MUST be executable via npm test or jest commands without requiring manual configuration changes

In scope:
- All TypeScript test files across all packages
- Unit tests for runtime components and utilities
- Integration tests for database adapters and client functionality
- Functional tests for issue reproduction and regression prevention
- End-to-end tests for complete user workflows
- Test infrastructure and helper utilities

Out of scope:
- Build-time validation scripts that are not tests
- Development tooling and scripts in scripts/ directories
- Documentation examples that demonstrate usage but are not automated tests
- Performance benchmarking tools (unless implemented as Jest tests)
- Manual testing procedures and QA checklists

Exceptions:
- EX-001: Legacy test files exist in a package being migrated from another testing framework
- EX-002: Specialized testing tools are required for specific scenarios (e.g., browser automation, load testing)

## Rationale

- Pattern detected across 27 files with 92.47% confidence indicates Jest is the de facto standard already established in the codebase
- Jest provides comprehensive TypeScript support, built-in mocking, snapshot testing, and excellent developer experience with watch mode and parallel execution
- Consistent test organization patterns (__tests__/, tests/, .test.ts) align with Jest conventions and community best practices
- Unified testing framework reduces cognitive load, simplifies CI/CD configuration, and enables shared test utilities across packages

## Consequences

Positive:
- Developers have consistent testing experience across all packages with familiar APIs and tooling
- Test infrastructure can be shared and reused, reducing duplication and maintenance burden
- CI/CD pipelines can use standardized test execution and reporting mechanisms
- New team members can quickly understand and contribute to test suites following established patterns
- Test coverage reporting and quality metrics can be aggregated consistently across the monorepo

Negative:
- Teams already using alternative frameworks must migrate existing tests, requiring time investment
- Jest may have performance limitations for very large test suites requiring optimization or sharding strategies
- Some specialized testing scenarios may require additional tooling or workarounds within Jest
- Framework lock-in makes future migration to alternative testing tools more costly

## Alternatives

- Allow multiple testing frameworks (Vitest, Mocha, AVA) based on team preference (rejected)
  Rejected because: Multiple frameworks create inconsistent developer experience, fragment test infrastructure, complicate CI/CD, and increase maintenance burden across packages
  When valid: Only valid for specialized testing scenarios requiring capabilities Jest cannot provide
- Adopt Vitest as the standard testing framework for better Vite integration and performance (rejected)
  Rejected because: Existing codebase has 27+ files already using Jest patterns; migration cost outweighs benefits, and Jest ecosystem is more mature for TypeScript projects
  When valid: Could be reconsidered if Vite becomes primary build tool and migration ROI is demonstrated
- Use native Node.js test runner (node:test) to eliminate external dependencies (rejected)
  Rejected because: Node.js test runner lacks maturity, ecosystem tooling, and advanced features like snapshot testing and comprehensive mocking that Jest provides
  When valid: May become viable in future Node.js versions with feature parity to Jest

## Risks

- Jest performance degradation as test suite grows, leading to slow CI/CD pipelines and poor developer experience
  Mitigation: Implement test sharding, parallel execution, and selective test running based on changed files; monitor test execution times and optimize slow tests
  Owner: Engineering team / DevOps
- Inconsistent Jest configurations across packages causing test behavior differences and debugging difficulties
  Mitigation: Create shared Jest configuration presets in root package; document configuration standards; implement linting for Jest config files
  Owner: Platform team
- Teams may bypass testing standards for quick fixes or under time pressure, degrading test quality over time
  Mitigation: Enforce test file naming and location via automated checks in CI; require test coverage thresholds; include testing standards in code review checklist
  Owner: Engineering leadership

## Implementation Notes

- Create shared Jest configuration preset in monorepo root that packages can extend with package-specific overrides
- Establish test directory templates and generators (e.g., plop, hygen) to scaffold new test files following conventions
- Document testing patterns and examples in developer guide covering unit, integration, functional, and e2e test organization
- Set up CI pipeline to run tests in parallel by package and test type, with appropriate timeouts and retry logic
- Configure IDE integrations (VS Code Jest extension) and provide setup instructions for optimal developer experience

## Continuation Context


Verify commands:
- find . -name '*.test.ts' -o -name 'test.ts' -o -name 'tests.ts' | head -20
- grep -r "from '@jest'" --include='*.ts' --include='*.json' | head -10
- find . -type d -name '__tests__' -o -name 'tests' | head -20
- cat package.json | grep -A 5 '"jest"' || cat jest.config.js || cat jest.config.ts

Accept when:
- Test files are found in __tests__/ or tests/ directories with .test.ts or test.ts naming conventions
- Jest dependencies or configuration are present in package.json or jest.config files
- Test directory structure shows organization by test type (integration/, functional/, e2e/, unit/)
- npm test or jest commands successfully execute test suites without manual configuration

## Enforcement

- Verified by: Automated CI checks verify test file naming conventions and directory structure
- Verified by: Code review checklist includes verification of Jest usage and test organization
- Verified by: Linting rules enforce test file patterns and Jest import statements
- Verified by: Pre-commit hooks validate test file locations match organizational standards
- Violation handling: CI pipeline fails if test files are found outside approved directories or with non-standard naming
- Violation handling: Pull requests with test violations are blocked from merging until corrected
- Violation handling: Automated comments on PRs highlight specific violations with links to testing standards documentation
- Violation handling: Quarterly audits identify and track remediation of legacy test files not following standards
- Exception process: Submit exception request via GitHub issue with template explaining rationale and scope
- Exception process: Tech lead or architect reviews exception request within 2 business days
- Exception process: Approved exceptions are documented in ADR addendum with expiration date or migration plan
- Exception process: Exception tracking dashboard monitors active exceptions and ensures timely resolution