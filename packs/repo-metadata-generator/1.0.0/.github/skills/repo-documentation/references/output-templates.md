# Output Templates — Phase 1 Per-Repository Artifacts

Structural templates for all 13 Phase 1 artifacts. Use these as the starting skeleton when generating each artifact.

---

## P1-1: `repo-identity.yaml`

```yaml
artifact: "P1-1"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
description: ""
owner:
  team: ""
  contacts: []
tech-stack:
  languages: []
  frameworks: []
  databases: []
  messaging: []
  cloud-provider: ""
repo-type: ""          # service | frontend | library | iac | pipeline
status: ""             # active | deprecated | archived
links:
  source: ""
  ci-cd: ""
  monitoring: ""
  dashboard: ""
  documentation: ""
```

---

## P1-2: `api/api-summary.yaml`

```yaml
artifact: "P1-2"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
api-type: ""           # rest | graphql | grpc | mixed
base-path: ""
spec-files:
  - path: ""
    format: ""         # openapi | graphql-schema | protobuf
endpoints:
  - path: ""
    method: ""
    summary: ""
    auth:
      required: true
      mechanism: ""
      scopes: []
    deprecated: false
```

---

## P1-3: `data-model/schema.yaml`

```yaml
artifact: "P1-3"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
storage-technology:
  type: ""             # relational | document | key-value | graph | time-series | search
  product: ""
  version: ""
entities:
  - name: ""
    description: ""
    table-name: ""
    fields:
      - name: ""
        type: ""
        nullable: false
        primary-key: false
        foreign-key: null
        classification: ""    # public | internal | sensitive | pii
        description: ""
    relationships:
      - target-entity: ""
        type: ""              # one-to-one | one-to-many | many-to-many
        foreign-key: ""
```

## P1-3: `data-model/data-model.md`

```markdown
# Data Model — {repo-name}

## Overview

Brief description of the data storage approach, technology, and key design decisions.

## Entity-Relationship Diagram

\```mermaid
erDiagram
    EntityA ||--o{ EntityB : "has many"
    EntityB }|--|| EntityC : "belongs to"
\```

## Entities

### {EntityName}

**Description**: ...
**Table**: `table_name`

| Field | Type | Nullable | PK | FK | Classification | Description |
|-------|------|----------|-----|-----|----------------|-------------|
| id | uuid | no | yes | - | internal | Primary identifier |

## Data Classification Summary

| Classification | Count | Examples |
|---------------|-------|---------|
| PII | N | email, phone |
| Sensitive | N | ... |
| Internal | N | ... |
| Public | N | ... |
```

---

## P1-4: `dependencies.yaml`

```yaml
artifact: "P1-4"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
internal-dependencies:
  - repo: ""
    direction: ""      # calls | called-by
    protocol: ""
    description: ""
external-dependencies:
  - name: ""
    type: ""           # saas | api | database | cdn | dns | other
    url: ""
    criticality: ""    # critical | high | medium | low
consumed-apis:
  - repo: ""
    endpoint: ""
    method: ""
produced-apis:
  - endpoint: ""
    method: ""
    consumers: []
consumed-events:
  - topic: ""
    schema: ""
    producer-repo: ""
produced-events:
  - topic: ""
    schema: ""
    consumer-repos: []
key-libraries:
  - name: ""
    version: ""
    purpose: ""
```

---

## P1-5: `events.yaml`

```yaml
artifact: "P1-5"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
broker:
  technology: ""       # kafka | rabbitmq | service-bus | sns-sqs | eventbridge | nats | pulsar
  version: ""
  connection-name: ""
published-events:
  - name: ""
    topic: ""
    schema-ref: ""
    payload-example: {}
    trigger: ""
    frequency: ""
consumed-events:
  - name: ""
    topic: ""
    schema-ref: ""
    handler-function: ""
    idempotent: false
    retry-policy: ""
```

---

## P1-6: `configuration.yaml`

```yaml
artifact: "P1-6"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
environment-variables:
  - name: ""
    purpose: ""
    sensitivity: ""    # public | internal | secret
    default: null
    required: true
feature-flags:
  - name: ""
    purpose: ""
    default: false
    source: ""
config-files:
  - path: ""
    purpose: ""
    format: ""         # json | yaml | toml | ini | env | xml | properties
secrets:
  - name: ""
    vault-source: ""
    purpose: ""
infrastructure-resources:
  - type: ""
    name: ""
    purpose: ""
```

---

## P1-7: `deployment.yaml`

```yaml
artifact: "P1-7"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
deployment-targets:
  - environment: ""    # dev | staging | prod | dr
    cloud-provider: ""
    service-type: ""   # kubernetes | app-service | lambda | ecs | vm | static | cdn
    region: ""
    namespace: ""
ci-cd:
  tool: ""
  pipeline-file: ""
  stages: []
  triggers:
    - event: ""
      branch: ""
scaling:
  min-instances: 1
  max-instances: 1
  auto-scaling-rules: []
health-checks:
  - type: ""           # liveness | readiness | startup
    path: ""
    interval-seconds: 30
deployment-strategy: "" # blue-green | canary | rolling | recreate | feature-flag
infrastructure-resources:
  - resource-type: ""
    name: ""
    sku: ""
```

## P1-7: `deployment.md`

```markdown
# Deployment — {repo-name}

## Overview

Brief deployment architecture description.

## Deployment Topology

\```mermaid
flowchart LR
    subgraph Prod["Production"]
        LB[Load Balancer] --> App1[Instance 1]
        LB --> App2[Instance 2]
        App1 --> DB[(Database)]
        App2 --> DB
    end
\```

## CI/CD Pipeline

| Stage | Tool | Trigger | Description |
|-------|------|---------|-------------|
| Build | ... | push to main | ... |
| Test | ... | after build | ... |
| Deploy | ... | after test | ... |

## Scaling

- **Min instances**: N
- **Max instances**: N
- **Auto-scaling rules**: ...

## Health Checks

| Type | Path | Interval |
|------|------|----------|
| Liveness | /health | 30s |

## Deployment Strategy

Description of the deployment strategy and rollback procedures.
```

---

## P1-8: `architecture.md`

```markdown
# Architecture — {repo-name}

## Overview

Brief description of the service architecture and key design decisions.

## Component Diagram

\```mermaid
flowchart TD
    subgraph Service["service-name"]
        API[API Layer]
        BL[Business Logic]
        DAL[Data Access]
        Cache[Cache Layer]
    end
    Client[Client] --> API
    API --> BL
    BL --> DAL
    BL --> Cache
    DAL --> DB[(Database)]
    Cache --> Redis[(Redis)]
\```

## Design Patterns

| Pattern | Where Used | Purpose |
|---------|-----------|---------|
| Repository | Data Access | Abstract data persistence |
| CQRS | Command/Query | Separate read/write paths |

## Entry Points

| Entry Point | Type | Description |
|-------------|------|-------------|
| /api/* | HTTP | REST API endpoints |
| queue-handler | Message | Async message processing |

## Request Flow

\```mermaid
sequenceDiagram
    Client->>API: HTTP Request
    API->>Auth: Validate token
    Auth-->>API: OK
    API->>Service: Process
    Service->>DB: Query
    DB-->>Service: Data
    Service-->>API: Response
    API-->>Client: HTTP Response
\```

## Error Handling & Retry Strategy

- **Transient failures**: Retry with exponential backoff (max 3 retries)
- **Circuit breaker**: Trips after 5 consecutive failures, half-open after 30s
- **Dead letter**: Failed messages sent to DLQ after max retries

## Caching Strategy

| Cache | Technology | TTL | Invalidation |
|-------|-----------|-----|-------------|
| API responses | Redis | 5m | On write |
| Config | In-memory | 1h | On restart |

## Observability

- **Logging**: Structured JSON logs via {framework}
- **Tracing**: OpenTelemetry with {exporter}
- **Metrics**: {tool} with custom business metrics
```

---

## P1-9: `security.yaml`

```yaml
artifact: "P1-9"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
authentication:
  mechanism: ""        # oauth2 | api-key | mtls | basic | saml | oidc | none
  provider: ""
  token-endpoint: ""
authorization:
  model: ""            # rbac | abac | acl | policy-based | none
  roles: []
  scopes: []
  policies: []
data-protection:
  encryption-at-rest: false
  encryption-in-transit: false
  key-management: ""
compliance-tags: []    # gdpr | soc2 | hipaa | pci-dss | iso27001
security-considerations:
  - description: ""
    severity: ""       # critical | high | medium | low | info
    status: ""         # open | mitigated | accepted | resolved
```

---

## P1-10: `domain.md`

```markdown
# Business Domain — {repo-name}

## Business Capability

What business capability does this service/app serve?

## Bounded Context

- **Context name**: ...
- **Core domain / Supporting / Generic**: ...

## Key Domain Entities

| Entity | Description | Business Rules |
|--------|-------------|----------------|
| Order | A customer purchase | Must have at least one line item |

## User-Facing Impact

How does this service affect end users? What user journeys does it participate in?

## Domain Dependencies

### Upstream (provides data/events to this context)
- {other-context}: provides {what}

### Downstream (consumes data/events from this context)
- {other-context}: consumes {what}
```

---

## P1-11: `operations.yaml` + `runbook.md`

### `operations.yaml`

```yaml
artifact: "P1-11"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
slis:
  - name: ""
    metric: ""
    target: ""
    measurement: ""
alerting-rules:
  - name: ""
    condition: ""
    threshold: ""
    severity: ""       # critical | warning | info
    notification-channel: ""
failure-modes:
  - mode: ""
    impact: ""
    remediation: ""
    automated-recovery: false
backup-recovery:
  backup-frequency: ""
  retention-period: ""
  rto: ""
  rpo: ""
escalation:
  on-call-team: ""
  escalation-path: []
```

### `runbook.md`

```markdown
# Runbook — {repo-name}

## Service Health Check

1. Check endpoint: `GET /health`
2. Verify response: `200 OK` with `{"status": "healthy"}`

## Common Issues

### Issue: {Description}

**Symptoms**: ...
**Cause**: ...
**Resolution**:
1. Step 1
2. Step 2

**Prevention**: ...

## Dependency Health

| Dependency | Health Check | Fallback |
|-----------|-------------|----------|
| Database | Connection pool check | N/A — critical |
| Cache | PING command | Bypass cache |

## Restart Procedure

1. ...
2. ...

## Scaling Procedure

1. ...
2. ...

## Backup & Recovery

- **Backup frequency**: ...
- **Recovery steps**: ...
```

---

## P1-12: `pipeline.yaml`

```yaml
artifact: "P1-12"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
sources:
  - name: ""
    type: ""           # database | api | file | stream | queue | other
    ingestion-method: ""
    repo-ref: ""
transformations:
  - name: ""
    description: ""
    technology: ""
sinks:
  - name: ""
    type: ""           # database | data-warehouse | data-lake | api | file | stream
    repo-ref: ""
schedule:
  type: ""             # cron | event-triggered | manual | continuous
  expression: ""
  timezone: ""
data-quality:
  - check: ""
    severity: ""       # fail | warn | info
volume:
  daily-records: ""
  daily-size: ""
retry-policy:
  max-retries: 3
  backoff: ""
  dead-letter: ""
```

---

## P1-13: `runtime-behavior.yaml` + `runtime-behavior.md`

### `runtime-behavior.yaml`

```yaml
artifact: "P1-13"
version: "1.0"
generated: "YYYY-MM-DD"
repo-name: ""
data-sources:
  - type: ""           # traces | logs | apm | metrics
    tool: ""
    time-range: ""
call-patterns:
  - caller: ""
    callee: ""
    protocol: ""
    calls-per-day: ""
    avg-latency-ms: 0
latency:
  p50-ms: 0
  p95-ms: 0
  p99-ms: 0
error-rates:
  overall-rate: ""
  top-errors:
    - error: ""
      rate: ""
      category: ""
resource-utilization:
  avg-cpu-percent: 0
  peak-cpu-percent: 0
  avg-memory-mb: 0
  peak-memory-mb: 0
hot-paths:
  - path: ""
    volume-per-day: ""
    avg-latency-ms: 0
trace-dependencies:
  - target: ""
    observed: true
    declared-in-p1-4: true
message-throughput:
  - topic: ""
    messages-per-day: ""
    avg-processing-ms: 0
```

### `runtime-behavior.md`

```markdown
# Runtime Behavior — {repo-name}

## Data Sources

| Source Type | Tool | Time Range |
|-----------|------|------------|
| Traces | ... | Last 30 days |
| Logs | ... | Last 30 days |

## Service Call Patterns

\```mermaid
flowchart TD
    ThisService["{repo-name}"]
    ThisService -->|"N calls/day, Xms avg"| ServiceA
    ThisService -->|"N calls/day, Xms avg"| ServiceB
    ServiceC -->|"N calls/day"| ThisService
\```

## Latency Profile

| Percentile | Latency |
|-----------|---------|
| p50 | Xms |
| p95 | Xms |
| p99 | Xms |

## Error Analysis

- **Overall error rate**: X%
- **Top errors**: ...

## Hot Paths

| Path | Volume/Day | Avg Latency |
|------|-----------|-------------|
| ... | ... | ... |

## Dependency Validation (Static vs. Runtime)

| Dependency | Declared (P1-4) | Observed (Traces) | Status |
|-----------|----------------|-------------------|--------|
| service-a | yes | yes | Confirmed |
| service-b | yes | no | Possibly stale |
| service-c | no | yes | Undocumented |
```
