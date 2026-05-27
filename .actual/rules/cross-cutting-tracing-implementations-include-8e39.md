# Adopt OpenTelemetry-Compatible Trace Context Propagation for Distributed Tracing: Tracing Implementations Include

These rules are ALWAYS ACTIVE for all distributed tracing implementations and trace context propagation mechanisms across the codebase, including client-side database operations, query execution paths, service boundaries, and asynchronous operations.

### Rules

- **R-OTEL-001** SHOULD: Tracing implementations SHOULD include comprehensive test coverage for span creation, context propagation, and parent-child relationships.

### Verify

```bash
# Check for OpenTelemetry dependencies
grep -r "@opentelemetry" packages/ --include="*.json" | grep -q "dependencies" && echo "OpenTelemetry dependency found"

# Count span creation and trace context usage patterns
grep -r "trace.*context\|span.*create\|startSpan" packages/ --include="*.ts" --include="*.js" | wc -l

# Count test files for tracing or span functionality
find packages/ -name "*trace*.test.ts" -o -name "*span*.test.ts" | xargs grep -l "describe\|it\|test" | wc -l
```

**Accept when:**
- OpenTelemetry packages are declared as dependencies in package.json files
- At least 10 instances of span creation or trace context usage are found across the codebase
- At least 1 test file exists specifically for tracing or span functionality with test cases

<enforcement>
Claude Code MUST NOT skip or defer verification. All three acceptance criteria must be satisfied before approving tracing implementations.
</enforcement>