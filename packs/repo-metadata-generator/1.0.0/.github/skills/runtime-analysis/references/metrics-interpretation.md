# Metrics Interpretation Procedures

How to interpret APM metrics to derive SLI baselines, resource utilization patterns, and bottleneck assessments.

---

## Key Metrics Categories

| Category | Metrics | Purpose |
|---|---|---|
| **Request throughput** | Requests/sec, requests/day, messages/sec | Volume and capacity |
| **Latency** | p50, p95, p99 response time | Performance baseline |
| **Error rate** | 4xx rate, 5xx rate, exception rate | Reliability baseline |
| **Resource utilization** | CPU %, memory MB/%, disk I/O, network I/O | Capacity assessment |
| **Saturation** | Queue depth, thread pool utilization, connection pool usage | Bottleneck indicators |

---

## Procedure: Derive SLI Baselines

### Step 1 — Identify Available Metrics

Check which APM/monitoring tool is in use:
- **Application Insights**: `requests`, `dependencies`, `exceptions`, `performanceCounters`
- **Datadog**: `trace.*`, `runtime.*`, `system.*` metrics
- **New Relic**: `Transactions`, `Errors`, `Apdex`
- **Prometheus**: Custom metric names (check service's metric exports)

### Step 2 — Compute Baselines

For each metric, compute over the observation period (minimum 7 days):

| Metric | Baseline Value | How to Compute |
|---|---|---|
| Availability | Normal uptime % | `(total_requests - 5xx_errors) / total_requests * 100` |
| Latency (p50) | Normal median response | 50th percentile of all request durations |
| Latency (p95) | Normal tail latency | 95th percentile |
| Latency (p99) | Worst-case typical | 99th percentile |
| Error rate | Normal error % | `5xx_errors / total_requests * 100` |
| Throughput | Normal request volume | Average requests/day, note peak vs. off-peak |

### Step 3 — Identify Patterns

- **Daily patterns**: Higher traffic during business hours?
- **Weekly patterns**: Lower on weekends?
- **Peak periods**: Specific hours/days with spike traffic?

Document these patterns — they inform capacity planning and alert threshold tuning.

---

## Procedure: Assess Resource Utilization

### CPU
- **Avg CPU %**: Normal operating load
- **Peak CPU %**: Maximum observed during peak traffic
- **Assessment**:
  - < 40% avg: Comfortable headroom
  - 40-70% avg: Monitor scaling needs
  - \> 70% avg: Consider scaling up/out
  - \> 90% peak: Risk of CPU throttling

### Memory
- **Avg memory MB**: Normal working set
- **Peak memory MB**: Maximum observed
- **Assessment**:
  - Steady growth over time → potential memory leak
  - Sudden spikes → correlate with specific requests or batch jobs
  - Near limit → risk of OOM kills

### Message Processing
- **Messages/day per topic**: Throughput baseline
- **Avg processing time ms**: Per-message latency
- **Queue depth trend**: Stable = good, growing = consumer falling behind
- **Consumer lag**: If available (Kafka), monitor lag per consumer group

---

## Procedure: Identify Bottlenecks

### Bottleneck Indicators

| Signal | Possible Bottleneck |
|---|---|
| High p99 but low p50 | Occasional slow dependency or resource contention |
| Growing queue depth | Consumer cannot keep up with publisher |
| High CPU, normal latency | CPU-bound processing (may be OK if designed this way) |
| Normal CPU, high latency | Waiting on external dependency (I/O bound) |
| Connection pool exhaustion | Database or downstream service cannot handle connection volume |
| Thread pool saturation | Too many concurrent requests for available threads |

### Assessment Checklist

For each service, rate these dimensions:

| Dimension | Status | Evidence |
|---|---|---|
| CPU headroom | OK / Warning / Critical | Avg X%, Peak X% |
| Memory headroom | OK / Warning / Critical | Avg X MB, Peak X MB |
| Latency SLI | OK / Warning / Critical | p99 = X ms (target: Y ms) |
| Error rate SLI | OK / Warning / Critical | X% (target: < Y%) |
| Throughput capacity | OK / Warning / Critical | Current X req/s, estimated max Y req/s |
| Dependency health | OK / Warning / Critical | Slowest dep: X (avg Y ms) |

---

## Query Templates

### Application Insights (Kusto)

```kusto
// Resource utilization
performanceCounters
| where timestamp > ago(30d)
| where cloud_RoleName == "{service-name}"
| where category == "Process" and name == "% Processor Time"
| summarize avg(value), max(value), percentile(value, 95)

// Throughput over time  
requests
| where timestamp > ago(30d)
| where cloud_RoleName == "{service-name}"
| summarize count() by bin(timestamp, 1h)
| summarize avg(count_), max(count_), percentile(count_, 95)

// Slowest dependencies
dependencies
| where timestamp > ago(30d)
| where cloud_RoleName == "{service-name}"
| summarize avg(duration), percentile(duration, 95), percentile(duration, 99), count() by target
| order by percentile_duration_99 desc
```

### Prometheus (PromQL)

```promql
# CPU utilization
avg(rate(container_cpu_usage_seconds_total{pod=~"{service-name}-.*"}[5m])) * 100

# Memory utilization
avg(container_memory_working_set_bytes{pod=~"{service-name}-.*"}) / 1024 / 1024

# Request rate
rate(http_requests_total{service="{service-name}"}[5m])

# Latency percentiles
histogram_quantile(0.99, rate(http_request_duration_seconds_bucket{service="{service-name}"}[5m]))
```

---

## Output Mapping

Map metrics to `runtime-behavior.yaml` fields:

```yaml
latency:
  p50-ms: {computed p50}
  p95-ms: {computed p95}
  p99-ms: {computed p99}
resource-utilization:
  avg-cpu-percent: {computed average}
  peak-cpu-percent: {computed peak}
  avg-memory-mb: {computed average}
  peak-memory-mb: {computed peak}
```

For system-wide P2-13, aggregate across all services and present as comparative tables and heatmaps.
