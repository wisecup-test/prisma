# Adopt OpenTelemetry-Compatible Trace Context Propagation for Distributed Tracing: Trace Context Propagated

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all distributed tracing implementations and trace context propagation mechanisms across the codebase.

## Context

- The system requires distributed tracing capabilities to track requests across multiple services and components, enabling performance analysis and debugging of complex interactions
- OpenTelemetry has emerged as the industry standard for observability, providing vendor-neutral APIs and instrumentation for traces, metrics, and logs
- Trace context propagation is essential for maintaining parent-child span relationships across service boundaries and asynchronous operations
- Evidence from sqlcommenter-trace-context and span.test.ts indicates active implementation and testing of trace context handling mechanisms
- The codebase demonstrates a need for standardized span creation, context propagation, and trace metadata management across query execution and client operations

## Problem Statement

Without a standardized approach to trace context propagation and span management, distributed tracing implementations become fragmented, leading to broken trace continuity, inconsistent metadata propagation, difficulty correlating operations across service boundaries, and reduced observability effectiveness in production environments.

## Decision

1. MUST: Trace context MUST be propagated across asynchronous boundaries, including database queries, HTTP requests, and inter-service communications

## Policy Block

- MUST Trace context MUST be propagated across asynchronous boundaries, including database queries, HTTP requests, and inter-service communications

In scope:
- All client-side database operations and query execution paths
- Query plan executor and distributed query processing components
- HTTP/gRPC service boundaries requiring trace propagation
- Asynchronous operations and background job processing
- Test suites validating tracing behavior and context propagation

Out of scope:
- Internal function calls within a single synchronous execution context that do not cross service boundaries
- Development-only debugging utilities not intended for production observability
- Third-party libraries that do not support OpenTelemetry instrumentation
- Legacy code scheduled for deprecation within the current release cycle

Exceptions:
- EXC-001: Performance-critical hot paths where tracing overhead exceeds 5% of execution time
- EXC-002: Integration with third-party systems that require proprietary tracing formats incompatible with OpenTelemetry

## Rationale

- Pattern detected with 93.15% confidence across 2 files (sqlcommenter-trace-context and span.test.ts), indicating deliberate architectural investment in tracing infrastructure
- OpenTelemetry provides vendor-neutral standardization, preventing lock-in to specific APM vendors and enabling flexibility in observability tooling choices
- SQLCommenter integration demonstrates commitment to propagating trace context into database layers, enabling correlation between application and database performance
- Comprehensive test coverage for span management indicates this is a production-critical capability requiring reliability guarantees

## Consequences

Positive:
- Unified observability across all services and components, enabling end-to-end request tracing and performance analysis
- Vendor-neutral implementation allows switching between APM providers (Datadog, New Relic, Honeycomb, etc.) without code changes
- Database query correlation through SQLCommenter enables identification of slow queries and their originating application contexts
- Standardized span semantics improve developer productivity by providing consistent tracing patterns across the codebase

Negative:
- Additional runtime overhead from span creation, context propagation, and trace export operations (typically 1-3% performance impact)
- Increased complexity in error handling to ensure spans are properly closed and errors are recorded in trace metadata
- Learning curve for developers unfamiliar with OpenTelemetry concepts and semantic conventions
- Potential for trace data volume to grow significantly, requiring careful sampling strategy and storage cost management

## Alternatives

- Use vendor-specific tracing SDKs (e.g., Datadog APM, New Relic Agent) directly without OpenTelemetry abstraction (rejected)
  Rejected because: Creates vendor lock-in, prevents multi-vendor observability strategies, and requires significant refactoring if switching APM providers
  When valid: Only valid for greenfield projects with long-term commitment to a single APM vendor and no multi-cloud requirements
- Implement custom tracing framework with proprietary context propagation format (rejected)
  Rejected because: Reinvents the wheel, lacks ecosystem tooling support, creates maintenance burden, and prevents integration with standard observability platforms
  When valid: Never recommended unless operating in highly specialized environments with unique security or compliance constraints
- Adopt structured logging with correlation IDs instead of distributed tracing (deferred)
  Rejected because: Provides correlation but lacks timing information, span relationships, and visual trace representations that distributed tracing offers
  When valid: Valid as a complementary approach for detailed event logging within spans, but insufficient as a complete replacement for tracing

## Risks

- Trace context propagation failures in edge cases (e.g., thread pool boundaries, event loops) could result in broken traces and lost observability
  Mitigation: Implement comprehensive integration tests covering all async boundaries, add monitoring for orphaned spans, and establish runbooks for trace debugging
  Owner: Platform Observability Team
- High-cardinality trace attributes or excessive span creation could lead to performance degradation or overwhelming trace backend storage
  Mitigation: Implement sampling strategies (head-based and tail-based), establish attribute cardinality limits, and monitor trace export performance metrics
  Owner: Engineering Team and SRE
- Incomplete adoption across the codebase could result in partial traces that provide limited debugging value
  Mitigation: Establish tracing coverage metrics, create instrumentation guidelines and examples, and include tracing requirements in code review checklists
  Owner: Architecture Review Board

## Implementation Notes

- Use the OpenTelemetry SDK for your language runtime and configure appropriate exporters (OTLP, Jaeger, Zipkin) based on your observability backend
- Wrap database client operations with span creation using semantic conventions (db.system, db.statement, db.operation attributes)
- For SQLCommenter integration, enable the trace context propagation middleware to inject traceparent comments into SQL queries
- Implement context propagation helpers for common async patterns (Promises, async/await, event emitters) to prevent context loss
- Configure sampling rates appropriately: 100% in development, 1-10% in production with tail-based sampling for errors and slow requests

## Continuation Context


Verify commands:
- grep -r "@opentelemetry" packages/ --include="*.json" | grep -q "dependencies" && echo "OpenTelemetry dependency found"
- grep -r "trace.*context\|span.*create\|startSpan" packages/ --include="*.ts" --include="*.js" | wc -l
- find packages/ -name "*trace*.test.ts" -o -name "*span*.test.ts" | xargs grep -l "describe\|it\|test" | wc -l

Accept when:
- OpenTelemetry packages are declared as dependencies in package.json files
- At least 10 instances of span creation or trace context usage are found across the codebase
- At least 1 test file exists specifically for tracing or span functionality with test cases

## Enforcement

- Verified by: Automated CI checks scanning for OpenTelemetry import statements and span creation patterns in new code
- Verified by: Code review checklist requiring tracing instrumentation for new service boundaries and database operations
- Verified by: Integration test suite validating trace context propagation across all major execution paths
- Violation handling: CI pipeline warnings for new service endpoints or database clients lacking tracing instrumentation
- Violation handling: Code review blocking for missing trace context propagation in cross-service calls
- Violation handling: Quarterly architecture review identifying gaps in tracing coverage with remediation plans
- Exception process: Submit exception request to Platform Observability Team with performance measurements or technical constraints
- Exception process: Architecture review board evaluates exception against observability requirements and approves/denies within 5 business days
- Exception process: Approved exceptions documented in ADR amendments with expiration dates and re-evaluation triggers