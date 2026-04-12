---
description: "Generate P1-13 runtime-behavior.yaml — runtime behavior profile documenting observed traffic patterns, latency percentiles, error rates, resource utilization, and dependency call graphs from production telemetry. Wave 2 artifact. Requires access to APM/observability data."
tools: [read, search, execute]
---

Generate **runtime-behavior.yaml** (P1-13) for the repository at `$input`.

## Applicability

This artifact applies to deployed `service` and `frontend` repos with production telemetry data available. If no observability data is accessible, document what instrumentation exists and mark metrics as `unavailable`.

## What to Analyze

1. **Traffic patterns**: Request rates (RPM/RPS), peak hours, seasonal patterns
2. **Latency**: p50, p95, p99 response times for key endpoints
3. **Error rates**: HTTP 5xx rate, exception types, error trends
4. **Resource utilization**: CPU, memory, disk I/O, network — average and peak
5. **Dependency calls**: Outbound call latency and error rates to upstream services, databases, caches
6. **Throughput**: Messages processed per second (for event-driven services)
7. **Auto-scaling behavior**: When scaling triggers fire, time to scale, max observed pods/instances

Use the `runtime-analysis` skill for detailed trace, log, and metrics interpretation procedures.

## Output

Write to `architects-metadata/phase1/{repo-name}/runtime-behavior.yaml`

```yaml
artifact: runtime-behavior
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"
observation-window: "{date range of data}"

traffic:
  avg-rpm: "{requests per minute}"
  peak-rpm: "{peak requests per minute}"
  peak-hours: "{time range UTC}"

latency:
  - endpoint: "{path or all}"
    p50-ms: "{value}"
    p95-ms: "{value}"
    p99-ms: "{value}"

error-rates:
  http-5xx-percent: "{percentage}"
  top-errors:
    - type: "{exception type}"
      frequency: "{count per day}"
      impact: "{user-facing|internal}"

resource-utilization:
  cpu:
    avg-percent: "{value}"
    peak-percent: "{value}"
  memory:
    avg-mb: "{value}"
    peak-mb: "{value}"

dependency-calls:
  - target: "{service or resource name}"
    avg-latency-ms: "{value}"
    error-rate-percent: "{value}"
    calls-per-minute: "{value}"

scaling:
  min-instances: "{value}"
  max-instances: "{value}"
  avg-instances: "{value}"
  scale-up-trigger: "{metric and threshold}"

availability:
  uptime-percent: "{value over window}"
  last-incident: "{date or none}"
```

## Validation

- All dependency calls should be consistent with P1-4 dependencies
- Latency values should be plausible (flag any p50 > p95 anomalies)
- If telemetry is unavailable, set values to `unavailable` with a note explaining why
