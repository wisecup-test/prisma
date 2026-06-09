# Adopt Event-Driven Database Adapter Integration Pattern: Client Code Not

These rules are ALWAYS ACTIVE for all database adapter implementations and client runtime request handling within the system, including adapter-neon, adapter-pg, RequestHandler.ts, and related integration test suites.

### Rules

- **R-EDAP-001** MUST_NOT: Client code MUST NOT directly depend on adapter-specific implementation details or connection primitives.

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
- No grep patterns detect direct coupling or missing event interfaces in adapter implementations
- Integration test suite execution passes with coverage at or above threshold

<enforcement>
Claude Code MUST NOT skip or defer verification of event-driven boundaries. All database adapter integrations MUST comply with R-EDAP-001. CI pipeline MUST fail if direct coupling is detected or integration tests do not meet coverage thresholds.
</enforcement>