# Adopt OpenTelemetry-Compatible Trace Context Propagation for Distributed Tracing: Sql Queries Include

These rules are ALWAYS ACTIVE for all client-side database operations, query execution paths, query plan executors, distributed query processing components, HTTP/gRPC service boundaries, asynchronous operations, and background job processing across the codebase.

### Rules

- **R-OTEL-SQL-001** SHOULD: SQL queries SHOULD include trace context metadata using SQLCommenter format when supported by the database driver.

### Verify

```bash
# Check for OpenTelemetry dependencies
grep -r "@opentelemetry" packages/ --include="*.json" | grep -q "dependencies" && echo "OpenTelemetry dependency found"

# Count trace context and span creation usage
grep -r "trace.*context\|span.*create\|startSpan" packages/ --include="*.ts" --include="*.js" | wc -l

# Count test files for tracing functionality
find packages/ -name "*trace*.test.ts" -o -name "*span*.test.ts" | xargs grep -l "describe\|it\|test" | wc -l
```

**Accept when:**
- OpenTelemetry packages are declared as dependencies in package.json files
- At least 10 instances of span creation or trace context usage are found across the codebase
- At least 1 test file exists specifically for tracing or span functionality with test cases

<enforcement>
Claude Code MUST NOT skip or defer verification. Verification is mandatory before accepting changes to database operations and query execution paths.
</enforcement>