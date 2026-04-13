# Log Analysis Procedures

How to extract error patterns, anomalies, and undocumented dependencies from structured logs.

---

## Supported Log Formats

| Source | Format | Key Fields |
|---|---|---|
| Structured JSON logs | JSON | `timestamp`, `level`, `message`, `service`, `traceId`, `error`, `stack` |
| Azure Monitor | Kusto | `AppTraces`, `AppExceptions` tables |
| ELK Stack | JSON via Elasticsearch | `@timestamp`, `level`, `message`, `service.name` |
| CloudWatch Logs | JSON/text | `timestamp`, `message`, structured fields |

---

## Procedure: Extract Error Patterns

### Step 1 — Query Error Logs

Filter logs where `level` is `ERROR`, `FATAL`, or `CRITICAL` for the target service over the observation period (minimum 7 days).

### Step 2 — Categorize Errors

Group errors by:
1. **Error type/class**: Exception class name or error code
2. **Error message pattern**: Group similar messages (ignore dynamic values like IDs, timestamps)
3. **Affected endpoint/handler**: Which API endpoint or message handler triggered the error

### Step 3 — Compute Rates

For each error category:
- **Count**: Total occurrences in the period
- **Rate**: Errors / total requests (or errors / time period)
- **Trend**: Increasing, stable, or decreasing over the observation window

### Step 4 — Identify Top Errors

Rank by occurrence count. The top 5-10 errors typically account for 80%+ of all errors.

---

## Procedure: Detect Anomalies

### Log Volume Anomalies
1. Compute hourly log volume over the observation period
2. Flag hours where volume exceeds 2× the rolling 7-day average (potential incident or traffic spike)
3. Correlate with deployment events (did a deploy happen just before the spike?)

### Error Rate Anomalies
1. Compute hourly error rate
2. Flag sustained periods (>1 hour) where error rate exceeds 2× the baseline
3. Cross-reference with P1-11 alerting rules (was an alert triggered?)

### New Error Types
1. Compare error types observed in the latest period vs. the previous period
2. Flag any new error types that didn't exist before
3. Correlate with recent code changes or dependency updates

---

## Procedure: Discover Undocumented Dependencies

### From Log Messages
1. Search for URLs, hostnames, or service names in log messages that are NOT declared in P1-4 `dependencies.yaml`
2. Common patterns:
   - `HttpClient` logging: "Request to https://{service}.internal/..."
   - Connection failure logs: "Failed to connect to {host}:{port}"
   - Timeout logs: "Timeout calling {service-name}"
3. Each discovered dependency should be flagged as "observed in logs, not declared"

### From Exception Stack Traces
1. Search for client library references in stack traces (e.g., `Redis.ConnectionMultiplexer`, `SqlConnection`, `KafkaProducer`)
2. These indicate infrastructure dependencies that may not be documented

---

## Query Templates

### Azure Monitor (Kusto)

```kusto
// Top errors for a service
AppExceptions
| where TimeGenerated > ago(30d)
| where AppRoleName == "{service-name}"
| summarize count() by ExceptionType, outerMessage
| order by count_ desc
| take 20

// Error rate over time
AppExceptions
| where TimeGenerated > ago(30d)
| where AppRoleName == "{service-name}"
| summarize errors=count() by bin(TimeGenerated, 1h)
| join kind=leftouter (
    AppRequests
    | where TimeGenerated > ago(30d)
    | where AppRoleName == "{service-name}"
    | summarize requests=count() by bin(TimeGenerated, 1h)
) on TimeGenerated
| extend errorRate = toreal(errors) / toreal(requests) * 100

// Undocumented dependency discovery
AppDependencies
| where TimeGenerated > ago(30d)
| where AppRoleName == "{service-name}"
| summarize count() by target, type
| order by count_ desc
```

### Elasticsearch (ELK)

```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "service.name": "{service-name}" } },
        { "match": { "level": "ERROR" } },
        { "range": { "@timestamp": { "gte": "now-30d" } } }
      ]
    }
  },
  "aggs": {
    "error_types": {
      "terms": { "field": "error.type.keyword", "size": 20 }
    }
  }
}
```

---

## Output Format

When populating `runtime-behavior.yaml`, map log analysis results to:

```yaml
error-rates:
  overall-rate: "X%"          # Errors / total events
  top-errors:
    - error: "TimeoutException"
      rate: "0.5%"
      category: "dependency-timeout"
    - error: "ValidationException" 
      rate: "0.3%"
      category: "client-error"
```

For anomalies and undocumented dependencies, include them in the `runtime-behavior.md` narrative section.
