---
name: runtime-analysis
description: "Analyze runtime observability data (distributed traces, structured logs, APM metrics) to generate runtime behavior documentation. Use when producing P1-13 runtime-behavior artifacts or P2-13 system runtime profiles. Covers OpenTelemetry, Jaeger, Zipkin, Application Insights, Datadog, New Relic trace analysis, structured log extraction, and APM metrics interpretation."
argument-hint: "Path to trace/log/metric exports or APM tool access details"
---

# Runtime Analysis

Analyze runtime observability data — distributed traces, structured logs, and APM metrics — to produce runtime behavior documentation for individual services (P1-13) and system-wide profiles (P2-13).

## When to Use

- Generating P1-13 Runtime Behavior Profile for a service
- Generating P2-13 System Runtime Profile across all services
- Validating P1-4 dependency declarations against observed runtime behavior
- Identifying undocumented dependencies, dead code paths, or real bottlenecks

## Data Source Requirements

| Data Type | Purpose | Common Tools |
|---|---|---|
| Distributed traces | Service call graphs, latency, hot paths | OpenTelemetry, Jaeger, Zipkin, Application Insights, Datadog APM, New Relic |
| Structured logs | Error patterns, anomalies, undocumented behaviors | ELK Stack, Splunk, Azure Monitor Logs, CloudWatch Logs |
| APM metrics | Resource utilization, SLI baselines, throughput | Application Insights, Datadog, New Relic, Prometheus + Grafana |

## Procedure

### For P1-13 (Single Service)

1. **Identify data sources** — Determine which observability tools are available for this service
2. **Extract trace data** — Follow [trace analysis procedures](./references/trace-analysis.md):
   - Service call patterns (who calls this service, who does it call)
   - Latency percentiles (p50, p95, p99)
   - Hot paths (highest-volume endpoints)
3. **Extract log data** — Follow [log analysis procedures](./references/log-analysis.md):
   - Error rates and top error categories
   - Anomaly patterns
   - Undocumented dependency signals
4. **Extract metrics** — Follow [metrics interpretation procedures](./references/metrics-interpretation.md):
   - CPU and memory utilization patterns
   - Request throughput
   - Message processing rates
5. **Validate dependencies** — Compare observed trace dependencies against P1-4 declared dependencies
6. **Write outputs** — Populate `runtime-behavior.yaml` and `runtime-behavior.md`

### For P2-13 (System-Wide)

1. **Aggregate all P1-13 outputs** across repos
2. **Build system-wide latency heatmap** — Service × Service matrix
3. **Build traffic flow diagram** — Mermaid with edge weights proportional to volume
4. **Identify system bottlenecks** — Highest p99, highest error rate, highest resource utilization
5. **Trace critical business paths** — End-to-end for key transactions
6. **Write output** — Populate `system-runtime-profile.md`

## Data Format Notes

If trace/log/metric data is provided as:
- **JSON exports**: Parse directly, extract relevant fields
- **CSV exports**: Parse as tabular data, map columns to schema
- **Screenshots/dashboard images**: Describe visually, note this is approximate data
- **API access**: Query the observability tool's API (provide query templates)
- **Manual input**: Accept user-provided values and note the source

When exact data is unavailable, document what is known and mark gaps with `data-source: "estimated"` or `data-source: "unavailable"`.
