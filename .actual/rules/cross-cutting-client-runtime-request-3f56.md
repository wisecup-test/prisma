# Adopt Event-Driven Database Adapter Integration Pattern: Client Runtime Request

These rules are ALWAYS ACTIVE for all database adapter implementations and client runtime request handling within the system, including adapter-neon, adapter-pg, RequestHandler.ts, and related integration test suites.

### Rules

- **R-EDAP-001** MUST: Client runtime request handlers MUST interact with database adapters through event-driven interfaces rather than direct synchronous method calls.

### Verify

```bash
# Verify all database adapters extend or implement event-driven interfaces
grep -r "extends.*EventEmitter\|implements.*EventEmitter" packages/adapter-*/src/*.ts

# Verify client runtime and adapters use event listeners and emitters
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