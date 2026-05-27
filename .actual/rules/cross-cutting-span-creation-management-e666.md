# Adopt OpenTelemetry-Compatible Trace Context Propagation for Distributed Tracing: Span Creation Management

These rules are ALWAYS ACTIVE for all distributed tracing implementations, trace context propagation mechanisms, client-side database operations, query execution paths, HTTP/gRPC service boundaries, and asynchronous operations across the codebase.

### Rules

- **R-OTEL-001** MUST: Span creation and management MUST follow OpenTelemetry semantic conventions for naming, attributes, and lifecycle management.

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
Claude Code MUST NOT skip or defer verification. All three acceptance criteria must be satisfied before approving code that implements distributed tracing.
</enforcement>