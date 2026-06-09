# Adopt Event-Driven Database Adapter Integration Pattern: Database Adapter Implementations

These rules are ALWAYS ACTIVE for all database adapter implementations and client runtime request handling within the system, including adapter-neon, adapter-pg, RequestHandler.ts, and related integration test suites.

### Rules

- **R-EDDA-001** MUST: All database adapter implementations (Neon, PostgreSQL, etc.) MUST conform to a common event-driven integration contract.
- **R-EDDA-002** MUST: All database adapter implementations MUST extend or implement event-driven interfaces with documented event contracts.
- **R-EDDA-003** MUST: Client runtime RequestHandler MUST interact with adapters through event listeners and emitters rather than direct method calls.
- **R-EDDA-004** MUST: Integration tests for each adapter MUST validate event emission, handling, and error propagation with >80% branch coverage.
- **R-EDDA-005** SHOULD: Define a clear event contract interface including connection lifecycle events (connect, disconnect, error), query execution events (query, result, error), and transaction events if applicable.
- **R-EDDA-006** SHOULD: Implement a shared test harness that validates event contract compliance for any adapter implementation, ensuring consistent behavior across all adapters.
- **R-EDDA-007** SHOULD: Use TypeScript interfaces or abstract classes to enforce event contract at compile time while allowing runtime flexibility in event handling.
- **R-EDDA-008** SHOULD: Document event flow diagrams showing typical request lifecycle through client runtime to adapter and back, including error scenarios.
- **R-EDDA-009** MAY: Consider implementing event tracing or correlation IDs to facilitate debugging across event boundaries in production.

### Verify

```bash
# Verify event-driven interface usage in adapter implementations
grep -r "extends.*EventEmitter\|implements.*EventEmitter" packages/adapter-*/src/*.ts

# Verify event listeners and emitters in client runtime and adapters
grep -r "on(\|emit(\|addEventListener" packages/client/src/runtime/RequestHandler.ts packages/adapter-*/src/*.ts

# Run integration tests with coverage threshold
npm test -- --testPathPattern="adapter.*test" --coverage --coverageThreshold='{"branches":80}'
```

**Accept when:**
- All database adapter implementations (neon.ts, pg.ts) extend or implement event-driven interfaces with documented event contracts
- Client runtime RequestHandler interacts with adapters through event listeners and emitters rather than direct method calls
- Integration tests for each adapter validate event emission, handling, and error propagation with >80% branch coverage
- Grep patterns confirm event-driven interface usage across all adapter implementations
- No direct adapter coupling is detected in client runtime code

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory for database adapter implementations and client runtime request handling. Violations must be caught by CI pipeline checks and code review before merge.
</enforcement>