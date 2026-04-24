# Trace Analysis Procedures

How to extract service call patterns, latency profiles, and hot paths from distributed traces.

---

## Supported Trace Formats

| Tool | Export Format | Key Fields |
|---|---|---|
| OpenTelemetry | OTLP JSON/Protobuf | `traceId`, `spanId`, `parentSpanId`, `operationName`, `serviceName`, `duration`, `status` |
| Jaeger | JSON | `traceID`, `spans[].operationName`, `spans[].duration`, `spans[].references` |
| Zipkin | JSON | `traceId`, `name`, `duration`, `localEndpoint.serviceName`, `parentId` |
| Application Insights | Kusto query results | `operation_Id`, `name`, `duration`, `target`, `resultCode` |
| Datadog | JSON/APM UI | `trace_id`, `span_id`, `resource`, `service`, `duration` |

---

## Procedure: Extract Service Call Patterns

### Step 1 — Identify Service-to-Service Calls

From trace data, extract all spans where:
- `span.kind` = `CLIENT` (outgoing call) or `SERVER` (incoming call)
- The target service differs from the source service

Build a list of edges: `{caller, callee, protocol, count, avg_latency}`

### Step 2 — Aggregate Call Volumes

Group by `(caller, callee)` and compute:
- **Total calls** in the observation period
- **Calls per day** (normalize by observation window)
- **Average latency** (mean of `duration` for those spans)

### Step 3 — Identify Hot Paths

Rank endpoints by:
1. **Volume**: Total number of traces hitting this endpoint
2. **Latency**: p95 or p99 response time
3. **Error rate**: Percentage of traces with error status

Hot paths = top 10 by volume OR any endpoint with p99 > 2× the service average.

---

## Procedure: Extract Latency Percentiles

### From Raw Spans

1. Filter spans where `span.kind = SERVER` and `serviceName = {target service}`
2. Collect all `duration` values
3. Sort and compute:
   - **p50**: Median (50th percentile)
   - **p95**: 95th percentile
   - **p99**: 99th percentile

### From APM Dashboards

If trace data is not available as raw exports, extract from APM dashboards:
- Application Insights: `requests | summarize percentile(duration, 50), percentile(duration, 95), percentile(duration, 99)`
- Datadog: Service page → Latency distribution
- New Relic: Distributed tracing → Latency histogram

---

## Procedure: Build Trace-Derived Dependency Graph

1. From all traces involving the target service, extract unique `(caller, callee)` pairs
2. Filter to edges where caller OR callee is the target service
3. For each edge, note whether the call was observed (from traces) vs. declared (from P1-4)
4. Produce a comparison table:

| Target | Observed in Traces | Declared in P1-4 | Status |
|--------|-------------------|------------------|--------|
| service-b | Yes | Yes | Confirmed |
| service-c | Yes | No | **Undocumented** |
| service-d | No | Yes | **Possibly stale** |

---

## Query Templates

### Application Insights (Kusto)

```kusto
// Service call patterns for a specific service
dependencies
| where timestamp > ago(30d)
| where cloud_RoleName == "{service-name}"
| summarize count(), avg(duration) by target, type
| order by count_ desc

// Latency percentiles
requests
| where timestamp > ago(30d)
| where cloud_RoleName == "{service-name}"
| summarize percentile(duration, 50), percentile(duration, 95), percentile(duration, 99)

// Hot paths
requests
| where timestamp > ago(30d)
| where cloud_RoleName == "{service-name}"
| summarize count(), avg(duration), percentile(duration, 99) by name
| order by count_ desc
| take 10
```

### Jaeger / OpenTelemetry

```
# Retrieve traces for a service (Jaeger API)
GET /api/traces?service={service-name}&limit=1000&lookback=720h

# Extract spans from trace results, filter by span.kind and service name
```

---

## Data Quality Considerations

- **Sampling**: Many tracing systems sample traces (e.g., 1/100). Volumes are estimates — multiply by sampling rate.
- **Async calls**: Message-based calls may not appear as parent-child spans. Look for `span.kind = PRODUCER` and `span.kind = CONSUMER` with matching trace IDs.
- **Time range**: Use at least 7 days of data to account for weekly patterns. 30 days is ideal.
- **Tail latency**: p99 is more meaningful than average for identifying outliers. p99.9 may be needed for critical paths.
