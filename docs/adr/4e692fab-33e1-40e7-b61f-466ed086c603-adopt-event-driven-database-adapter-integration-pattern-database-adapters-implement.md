# Adopt Event-Driven Database Adapter Integration Pattern: Database Adapters Implement

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all database adapter implementations and client runtime request handling within the system.

## Context

- The system requires integration between client runtime request handlers and multiple database adapters (Neon, PostgreSQL) with different connection models and capabilities
- Event-driven boundaries enable loose coupling between client request handling and database adapter implementations, allowing adapters to be swapped or extended without modifying client code
- Database operations are inherently asynchronous and benefit from event-driven patterns that handle connection lifecycle events, query execution events, and error propagation
- The pattern was detected across 6 files including client runtime handlers, adapter implementations (neon.ts, pg.ts), and comprehensive test suites, indicating systematic adoption
- Integration testing requirements necessitate observable event boundaries to validate adapter behavior and error handling in isolation

## Problem Statement

How should the system integrate client runtime request handling with multiple database adapter implementations while maintaining loose coupling, supporting asynchronous operations, enabling testability, and allowing adapter implementations to evolve independently without breaking client contracts?

## Decision

1. MUST: Database adapters MUST implement event-driven boundaries for connection lifecycle management, query execution, and error propagation

## Policy Block

- MUST Database adapters MUST implement event-driven boundaries for connection lifecycle management, query execution, and error propagation

In scope:
- All database adapter implementations (adapter-neon, adapter-pg, future adapters)
- Client runtime request handling layer (RequestHandler.ts and related components)
- Integration test suites that validate adapter behavior
- Error propagation and handling between client and adapter layers

Out of scope:
- Internal implementation details within individual adapters
- Database-specific query optimization strategies
- Connection pooling mechanisms internal to adapters
- Application-level business logic above the client runtime layer

Exceptions:
- EXC-001: Legacy adapter implementations during migration period require temporary direct coupling
- EXC-002: Performance-critical paths require direct adapter access for optimization

## Rationale

- Pattern detected with 90.77% confidence across 6 files spanning client runtime, multiple adapter implementations, and test suites, indicating systematic architectural adoption
- Event-driven boundaries enable the system to support multiple database adapters (Neon, PostgreSQL) with different connection models while maintaining a unified client interface
- The facet 'boundaries.event_driven' combined with integration pattern category indicates intentional architectural separation between client and adapter concerns
- Test files in both adapter packages demonstrate that event-driven boundaries facilitate isolated testing and validation of adapter behavior without requiring full system integration

## Consequences

Positive:
- Loose coupling between client runtime and database adapters enables independent evolution and testing of each layer
- New database adapters can be added by implementing the event-driven contract without modifying client code
- Event boundaries provide natural observation points for monitoring, logging, and debugging database interactions
- Asynchronous event-driven model aligns naturally with database operation semantics and enables better resource utilization

Negative:
- Event-driven architecture introduces additional complexity compared to direct synchronous method calls
- Debugging across event boundaries may require additional tooling and tracing infrastructure
- Performance overhead from event emission and handling may impact latency-sensitive operations
- Developers must understand event-driven patterns and async flow control, increasing learning curve

## Alternatives

- Direct synchronous adapter interface with abstract base class (rejected)
  Rejected because: Synchronous interfaces do not align with asynchronous database operations and would require blocking or complex callback patterns. Tight coupling through inheritance makes adapter swapping and testing more difficult.
  When valid: Only valid for simple synchronous data sources that do not require connection lifecycle management
- Dependency injection with adapter interfaces but no event boundaries (rejected)
  Rejected because: While dependency injection provides loose coupling, lack of event boundaries makes it difficult to observe and test adapter lifecycle events, connection management, and error propagation in isolation.
  When valid: Could be valid for simpler systems with single adapter and no complex lifecycle requirements
- Message queue-based integration with external broker (rejected)
  Rejected because: External message broker adds operational complexity and latency for in-process integration. Event-driven boundaries provide similar decoupling benefits without external dependencies.
  When valid: Valid for distributed systems where client and adapters run in separate processes or services

## Risks

- Event boundary abstraction may not accommodate all database-specific features, forcing workarounds or contract violations
  Mitigation: Design event contract with extension points for adapter-specific capabilities. Review contract with each new adapter implementation and evolve as needed.
  Owner: Architecture team and adapter maintainers
- Performance overhead from event emission could impact high-throughput scenarios
  Mitigation: Implement performance benchmarks in CI. Profile event handling overhead. Consider fast-path optimizations for critical operations while maintaining event boundary for observability.
  Owner: Performance engineering team
- Inconsistent event handling across different adapter implementations could lead to subtle integration bugs
  Mitigation: Maintain comprehensive integration test suite that validates event contract compliance for all adapters. Use shared test harness to ensure consistent behavior.
  Owner: QA and adapter development teams

## Implementation Notes

- Define a clear event contract interface that all database adapters must implement, including connection lifecycle events (connect, disconnect, error), query execution events (query, result, error), and transaction events if applicable
- Implement a shared test harness that validates event contract compliance for any adapter implementation, ensuring consistent behavior across Neon, PostgreSQL, and future adapters
- Use TypeScript interfaces or abstract classes to enforce event contract at compile time while allowing runtime flexibility in event handling
- Document event flow diagrams showing typical request lifecycle through client runtime to adapter and back, including error scenarios
- Consider implementing event tracing or correlation IDs to facilitate debugging across event boundaries in production

## Continuation Context


Verify commands:
- grep -r "extends.*EventEmitter\|implements.*EventEmitter" packages/adapter-*/src/*.ts
- grep -r "on(\|emit(\|addEventListener" packages/client/src/runtime/RequestHandler.ts packages/adapter-*/src/*.ts
- npm test -- --testPathPattern="adapter.*test" --coverage --coverageThreshold='{"branches":80}'

Accept when:
- All database adapter implementations (neon.ts, pg.ts) extend or implement event-driven interfaces with documented event contracts
- Client runtime RequestHandler interacts with adapters through event listeners and emitters rather than direct method calls
- Integration tests for each adapter validate event emission, handling, and error propagation with >80% branch coverage

## Enforcement

- Verified by: Automated CI checks using grep patterns to verify event-driven interface usage in adapter implementations
- Verified by: Integration test suite execution requiring >80% coverage of event handling paths
- Verified by: Code review checklist requiring verification of event contract compliance for new adapters
- Verified by: Static analysis tools checking for direct adapter coupling in client runtime code
- Violation handling: CI pipeline fails if grep patterns detect direct coupling or missing event interfaces
- Violation handling: Pull requests blocked if integration tests fail or coverage drops below threshold
- Violation handling: Architecture review required for any adapter implementation that cannot conform to event contract
- Violation handling: Violations documented as technical debt with remediation timeline if exception granted
- Exception process: Submit exception request to architecture team with justification (performance, legacy migration, etc.)
- Exception process: Provide benchmarks or migration timeline demonstrating necessity of exception
- Exception process: Document exception in ADR exceptions section with approval date and remediation plan
- Exception process: Schedule quarterly review of active exceptions to track remediation progress