---
description: "Generate P1-11 operations.yaml — operational profile documenting monitoring, alerting, logging, SLOs, incident response procedures, runbooks, and on-call information. Wave 4 artifact. Applies to deployed services."
tools: [read, search, execute]
---

Generate **operations.yaml** (P1-11) for the repository at `$input`.

## Applicability

This artifact applies to repos classified as `service` or `frontend` that have deployment configurations. Skip for `library` and `iac` repos.

## What to Analyze

1. **Monitoring**: Application Insights, Prometheus metrics, Datadog, New Relic — instrumentation in code
2. **Alerting**: Alert rules, PagerDuty/OpsGenie integrations, alert definitions in IaC or monitoring config
3. **Logging**: Log framework (Serilog, log4j, Winston), structured logging patterns, log levels, log destinations
4. **Health checks**: Health endpoints, dependency health checks, readiness vs. liveness
5. **SLOs/SLIs**: Documented service level objectives, SLI definitions, error budgets
6. **Runbooks**: Operational procedures in `docs/`, `runbooks/`, or wiki references
7. **Incident response**: Escalation paths, on-call schedules, incident classification

## Output

Write to `architects-metadata/phase1/{repo-name}/operations.yaml`

```yaml
artifact: operations
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

monitoring:
  platform: "{App-Insights|Prometheus|Datadog|New-Relic|mixed}"
  custom-metrics: ["{metric-name}"]
  dashboards: ["{dashboard name or URL reference}"]

logging:
  framework: "{Serilog|log4j|Winston|NLog|etc}"
  structured: true|false
  destinations: ["{console|file|App-Insights|ELK|Splunk}"]
  correlation-id: true|false

alerting:
  platform: "{PagerDuty|OpsGenie|Azure-Monitor|Grafana|etc}"
  rules:
    - name: "{alert name}"
      condition: "{trigger condition}"
      severity: "{critical|warning|info}"
      notification: "{channel or team}"

health-checks:
  endpoint: "{path}"
  checks: ["{dependency checks}"]

slos:
  - name: "{SLO name}"
    target: "{percentage}"
    sli: "{metric definition}"
    window: "{time window}"

runbooks:
  - name: "{procedure name}"
    location: "{file path or URL}"
    trigger: "{when to use}"

on-call:
  rotation: "{schedule description}"
  escalation: "{escalation path}"
```

## Validation

- All monitoring instrumentation found in code must be documented
- Health check paths must match actual endpoints
- Alert rules should reference real metrics
