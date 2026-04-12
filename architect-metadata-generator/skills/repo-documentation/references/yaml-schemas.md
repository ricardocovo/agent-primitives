# YAML Schema Definitions — Phase 1 Artifacts

JSON Schema definitions for all YAML artifacts produced during Phase 1 per-repository documentation.

---

## P1-1: `repo-identity.yaml`

```yaml
# JSON Schema
type: object
required: [artifact, version, generated, repo-name, description, owner, tech-stack, repo-type, status]
properties:
  artifact:
    const: "P1-1"
  version:
    type: string
  generated:
    type: string
    format: date
  repo-name:
    type: string
    pattern: "^[a-z0-9-]+$"
  description:
    type: string
  owner:
    type: object
    required: [team]
    properties:
      team: { type: string }
      contacts: { type: array, items: { type: string } }
  tech-stack:
    type: object
    properties:
      languages: { type: array, items: { type: string } }
      frameworks: { type: array, items: { type: string } }
      databases: { type: array, items: { type: string } }
      messaging: { type: array, items: { type: string } }
      cloud-provider: { type: string }
  repo-type:
    enum: [service, frontend, library, iac, pipeline]
  status:
    enum: [active, deprecated, archived]
  links:
    type: object
    properties:
      source: { type: string, format: uri }
      ci-cd: { type: string, format: uri }
      monitoring: { type: string, format: uri }
      dashboard: { type: string, format: uri }
      documentation: { type: string, format: uri }
```

---

## P1-2: `api-summary.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name, api-type, endpoints]
properties:
  artifact:
    const: "P1-2"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  api-type:
    enum: [rest, graphql, grpc, mixed]
  base-path: { type: string }
  spec-files:
    type: array
    items:
      type: object
      properties:
        path: { type: string }
        format: { enum: [openapi, graphql-schema, protobuf] }
  endpoints:
    type: array
    items:
      type: object
      required: [path, method, auth]
      properties:
        path: { type: string }
        method: { enum: [GET, POST, PUT, PATCH, DELETE, SUBSCRIPTION, QUERY, MUTATION, RPC] }
        summary: { type: string }
        auth:
          type: object
          properties:
            required: { type: boolean }
            mechanism: { type: string }
            scopes: { type: array, items: { type: string } }
        request-schema: { type: string }
        response-schema: { type: string }
        deprecated: { type: boolean }
```

---

## P1-3: `data-model/schema.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name, storage-technology, entities]
properties:
  artifact:
    const: "P1-3"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  storage-technology:
    type: object
    properties:
      type: { enum: [relational, document, key-value, graph, time-series, search] }
      product: { type: string }
      version: { type: string }
  entities:
    type: array
    items:
      type: object
      required: [name, fields]
      properties:
        name: { type: string }
        description: { type: string }
        table-name: { type: string }
        fields:
          type: array
          items:
            type: object
            required: [name, type, classification]
            properties:
              name: { type: string }
              type: { type: string }
              nullable: { type: boolean }
              primary-key: { type: boolean }
              foreign-key: { type: string }
              classification: { enum: [public, internal, sensitive, pii] }
              description: { type: string }
        relationships:
          type: array
          items:
            type: object
            properties:
              target-entity: { type: string }
              type: { enum: [one-to-one, one-to-many, many-to-many] }
              foreign-key: { type: string }
```

---

## P1-4: `dependencies.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name]
properties:
  artifact:
    const: "P1-4"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  internal-dependencies:
    type: array
    items:
      type: object
      required: [repo, direction]
      properties:
        repo: { type: string }
        direction: { enum: [calls, called-by] }
        protocol: { type: string }
        description: { type: string }
  external-dependencies:
    type: array
    items:
      type: object
      required: [name]
      properties:
        name: { type: string }
        type: { enum: [saas, api, database, cdn, dns, other] }
        url: { type: string }
        criticality: { enum: [critical, high, medium, low] }
  consumed-apis:
    type: array
    items:
      type: object
      properties:
        repo: { type: string }
        endpoint: { type: string }
        method: { type: string }
  produced-apis:
    type: array
    items:
      type: object
      properties:
        endpoint: { type: string }
        method: { type: string }
        consumers: { type: array, items: { type: string } }
  consumed-events:
    type: array
    items:
      type: object
      properties:
        topic: { type: string }
        schema: { type: string }
        producer-repo: { type: string }
  produced-events:
    type: array
    items:
      type: object
      properties:
        topic: { type: string }
        schema: { type: string }
        consumer-repos: { type: array, items: { type: string } }
  key-libraries:
    type: array
    items:
      type: object
      properties:
        name: { type: string }
        version: { type: string }
        purpose: { type: string }
```

---

## P1-5: `events.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name, broker]
properties:
  artifact:
    const: "P1-5"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  broker:
    type: object
    properties:
      technology: { enum: [kafka, rabbitmq, service-bus, sns-sqs, eventbridge, nats, pulsar, other] }
      version: { type: string }
      connection-name: { type: string }
  published-events:
    type: array
    items:
      type: object
      required: [name, topic]
      properties:
        name: { type: string }
        topic: { type: string }
        schema-ref: { type: string }
        payload-example: { type: object }
        trigger: { type: string }
        frequency: { type: string }
  consumed-events:
    type: array
    items:
      type: object
      required: [name, topic]
      properties:
        name: { type: string }
        topic: { type: string }
        schema-ref: { type: string }
        handler-function: { type: string }
        idempotent: { type: boolean }
        retry-policy: { type: string }
```

---

## P1-6: `configuration.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name]
properties:
  artifact:
    const: "P1-6"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  environment-variables:
    type: array
    items:
      type: object
      required: [name, purpose, sensitivity]
      properties:
        name: { type: string }
        purpose: { type: string }
        sensitivity: { enum: [public, internal, secret] }
        default: { type: string }
        required: { type: boolean }
  feature-flags:
    type: array
    items:
      type: object
      required: [name, purpose]
      properties:
        name: { type: string }
        purpose: { type: string }
        default: { type: boolean }
        source: { type: string }
  config-files:
    type: array
    items:
      type: object
      properties:
        path: { type: string }
        purpose: { type: string }
        format: { enum: [json, yaml, toml, ini, env, xml, properties] }
  secrets:
    type: array
    items:
      type: object
      required: [name, vault-source]
      properties:
        name: { type: string }
        vault-source: { type: string }
        purpose: { type: string }
  infrastructure-resources:
    type: array
    items:
      type: object
      properties:
        type: { type: string }
        name: { type: string }
        purpose: { type: string }
```

---

## P1-7: `deployment.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name, deployment-targets]
properties:
  artifact:
    const: "P1-7"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  deployment-targets:
    type: array
    items:
      type: object
      properties:
        environment: { enum: [dev, staging, prod, dr] }
        cloud-provider: { type: string }
        service-type: { enum: [kubernetes, app-service, lambda, ecs, vm, static, cdn, other] }
        region: { type: string }
        namespace: { type: string }
  ci-cd:
    type: object
    properties:
      tool: { type: string }
      pipeline-file: { type: string }
      stages: { type: array, items: { type: string } }
      triggers:
        type: array
        items:
          type: object
          properties:
            event: { type: string }
            branch: { type: string }
  scaling:
    type: object
    properties:
      min-instances: { type: integer }
      max-instances: { type: integer }
      auto-scaling-rules: { type: array, items: { type: string } }
  health-checks:
    type: array
    items:
      type: object
      properties:
        type: { enum: [liveness, readiness, startup] }
        path: { type: string }
        interval-seconds: { type: integer }
  deployment-strategy:
    enum: [blue-green, canary, rolling, recreate, feature-flag]
  infrastructure-resources:
    type: array
    items:
      type: object
      properties:
        resource-type: { type: string }
        name: { type: string }
        sku: { type: string }
```

---

## P1-9: `security.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name]
properties:
  artifact:
    const: "P1-9"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  authentication:
    type: object
    properties:
      mechanism: { enum: [oauth2, api-key, mtls, basic, saml, oidc, none] }
      provider: { type: string }
      token-endpoint: { type: string }
  authorization:
    type: object
    properties:
      model: { enum: [rbac, abac, acl, policy-based, none] }
      roles: { type: array, items: { type: string } }
      scopes: { type: array, items: { type: string } }
      policies: { type: array, items: { type: string } }
  data-protection:
    type: object
    properties:
      encryption-at-rest: { type: boolean }
      encryption-in-transit: { type: boolean }
      key-management: { type: string }
  compliance-tags:
    type: array
    items:
      enum: [gdpr, soc2, hipaa, pci-dss, iso27001, fedramp, other]
  security-considerations:
    type: array
    items:
      type: object
      properties:
        description: { type: string }
        severity: { enum: [critical, high, medium, low, info] }
        status: { enum: [open, mitigated, accepted, resolved] }
```

---

## P1-11: `operations.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name]
properties:
  artifact:
    const: "P1-11"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  slis:
    type: array
    items:
      type: object
      required: [name, metric]
      properties:
        name: { type: string }
        metric: { type: string }
        target: { type: string }
        measurement: { type: string }
  alerting-rules:
    type: array
    items:
      type: object
      properties:
        name: { type: string }
        condition: { type: string }
        threshold: { type: string }
        severity: { enum: [critical, warning, info] }
        notification-channel: { type: string }
  failure-modes:
    type: array
    items:
      type: object
      properties:
        mode: { type: string }
        impact: { type: string }
        remediation: { type: string }
        automated-recovery: { type: boolean }
  backup-recovery:
    type: object
    properties:
      backup-frequency: { type: string }
      retention-period: { type: string }
      rto: { type: string }
      rpo: { type: string }
  escalation:
    type: object
    properties:
      on-call-team: { type: string }
      escalation-path: { type: array, items: { type: string } }
```

---

## P1-12: `pipeline.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name, sources, sinks]
properties:
  artifact:
    const: "P1-12"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  sources:
    type: array
    items:
      type: object
      required: [name, type]
      properties:
        name: { type: string }
        type: { enum: [database, api, file, stream, queue, other] }
        ingestion-method: { type: string }
        repo-ref: { type: string }
  transformations:
    type: array
    items:
      type: object
      properties:
        name: { type: string }
        description: { type: string }
        technology: { type: string }
  sinks:
    type: array
    items:
      type: object
      required: [name, type]
      properties:
        name: { type: string }
        type: { enum: [database, data-warehouse, data-lake, api, file, stream, other] }
        repo-ref: { type: string }
  schedule:
    type: object
    properties:
      type: { enum: [cron, event-triggered, manual, continuous] }
      expression: { type: string }
      timezone: { type: string }
  data-quality:
    type: array
    items:
      type: object
      properties:
        check: { type: string }
        severity: { enum: [fail, warn, info] }
  volume:
    type: object
    properties:
      daily-records: { type: string }
      daily-size: { type: string }
  retry-policy:
    type: object
    properties:
      max-retries: { type: integer }
      backoff: { type: string }
      dead-letter: { type: string }
```

---

## P1-13: `runtime-behavior.yaml`

```yaml
type: object
required: [artifact, version, generated, repo-name]
properties:
  artifact:
    const: "P1-13"
  version: { type: string }
  generated: { type: string, format: date }
  repo-name: { type: string }
  data-sources:
    type: array
    items:
      type: object
      properties:
        type: { enum: [traces, logs, apm, metrics] }
        tool: { type: string }
        time-range: { type: string }
  call-patterns:
    type: array
    items:
      type: object
      properties:
        caller: { type: string }
        callee: { type: string }
        protocol: { type: string }
        calls-per-day: { type: string }
        avg-latency-ms: { type: number }
  latency:
    type: object
    properties:
      p50-ms: { type: number }
      p95-ms: { type: number }
      p99-ms: { type: number }
  error-rates:
    type: object
    properties:
      overall-rate: { type: string }
      top-errors:
        type: array
        items:
          type: object
          properties:
            error: { type: string }
            rate: { type: string }
            category: { type: string }
  resource-utilization:
    type: object
    properties:
      avg-cpu-percent: { type: number }
      peak-cpu-percent: { type: number }
      avg-memory-mb: { type: number }
      peak-memory-mb: { type: number }
  hot-paths:
    type: array
    items:
      type: object
      properties:
        path: { type: string }
        volume-per-day: { type: string }
        avg-latency-ms: { type: number }
  trace-dependencies:
    type: array
    items:
      type: object
      properties:
        target: { type: string }
        observed: { type: boolean }
        declared-in-p1-4: { type: boolean }
  message-throughput:
    type: array
    items:
      type: object
      properties:
        topic: { type: string }
        messages-per-day: { type: string }
        avg-processing-ms: { type: number }
```
