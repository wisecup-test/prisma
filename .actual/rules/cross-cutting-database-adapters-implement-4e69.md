# Adopt Event-Driven Database Adapter Integration Pattern: Database Adapters Implement

These rules are ALWAYS ACTIVE for all database adapter implementations and client runtime request handling within the system, including adapter-neon, adapter-pg, RequestHandler.ts, and related integration test suites.

### Rules

- **R-EVDA-001** MUST: Database adapters MUST implement event-driven boundaries for connection lifecycle management, query execution, and error propagation.

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

<enforcement>
Claude Code MUST NOT skip or defer verification. All three verification commands must pass before accepting changes to database adapter implementations or client runtime request handling.
</enforcement>