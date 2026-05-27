# Adopt OpenTelemetry-Compatible Trace Context Propagation for Distributed Tracing: Components Implement Custom

These rules are ALWAYS ACTIVE for all distributed tracing implementations and trace context propagation mechanisms across the codebase, including client-side database operations, query execution paths, service boundaries, asynchronous operations, and test suites validating tracing behavior.

### Rules

- **R-OTEL-001** MAY: Components MAY implement custom span processors or exporters for specialized tracing requirements beyond standard OpenTelemetry capabilities.

### Verify

```bash
# Check for OpenTelemetry dependencies
grep -r "@opentelemetry" packages/ --include="*.json" | grep -q "dependencies" && echo "OpenTelemetry dependency found"

# Count span creation and trace context usage instances
grep -r "trace.*context\|span.*create\|startSpan" packages/ --include="*.ts" --include="*.js" | wc -l

# Count test files for tracing or span functionality
find packages/ -name "*trace*.test.ts" -o -name "*span*.test.ts" | xargs grep -l "describe\|it\|test" | wc -l
```

**Accept when:**
- OpenTelemetry packages are declared as dependencies in package.json files
- At least 10 instances of span creation or trace context usage are found across the codebase
- At least 1 test file exists specifically for tracing or span functionality with test cases

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated CI checks MUST scan for OpenTelemetry import statements and span creation patterns in new code. Code review MUST enforce tracing instrumentation for new service boundaries and database operations. Integration tests MUST validate trace context propagation across all major execution paths.
</enforcement>