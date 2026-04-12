# Output Templates — Phase 2 System-Level Documents

Structural templates for all 13 Phase 2 documents. Use these as the starting skeleton when generating each document.

---

## P2-1: `system-architecture.md`

```markdown
# System Architecture Overview

## Technology Landscape

| Technology | Category | Used By (count) |
|-----------|----------|-----------------|
| ... | Language | N repos |
| ... | Framework | N repos |

## System Architecture Diagram

\```mermaid
flowchart TD
    subgraph Frontends["Frontend Layer"]
        ...
    end
    subgraph Services["Service Layer"]
        subgraph DomainA["Domain A"]
            ...
        end
        subgraph DomainB["Domain B"]
            ...
        end
    end
    subgraph Data["Data Layer"]
        ...
    end
    subgraph Infra["Infrastructure"]
        ...
    end
\```

## Bounded Context Map

\```mermaid
flowchart TD
    subgraph ContextA["Context A"]
        ...
    end
    subgraph ContextB["Context B"]
        ...
    end
    ContextA -->|"upstream"| ContextB
\```

## Key Design Patterns

| Pattern | Services Using It | Description |
|---------|------------------|-------------|
| CQRS | service-a, service-b | ... |
```

---

## P2-2: `service-catalog.md`

```markdown
# Service Catalog

## Summary

| Metric | Count |
|--------|-------|
| Total Repositories | N |
| Active Services | N |
| Frontend Apps | N |
| Shared Libraries | N |
| IaC Repos | N |
| Data Pipelines | N |

## By Team

### {Team Name}

| Repo | Type | Status | Tech Stack | Description |
|------|------|--------|-----------|-------------|
| [repo-name](../phase1/repo-name/repo-identity.yaml) | service | active | Node.js, PostgreSQL | ... |

## By Domain

### {Domain Name}

| Repo | Type | Owner | Description |
|------|------|-------|-------------|
| ... | ... | ... | ... |
```

---

## P2-3: `api-catalog.md`

```markdown
# API Catalog

## API Inventory

| Service | Base Path | Type | Endpoints | Auth |
|---------|-----------|------|-----------|------|
| order-service | /api/orders | REST | 12 | OAuth2 |

## API Dependency Matrix

| Consumer ↓ / Producer → | order-service | payment-service | user-service |
|--------------------------|---------------|-----------------|-------------|
| **frontend-app** | GET /orders | POST /payments | GET /users |
| **order-service** | - | POST /payments | GET /users |

## Deprecation Status

| Service | Endpoint | Status | Replacement | Sunset Date |
|---------|----------|--------|-------------|-------------|
| ... | ... | deprecated | ... | ... |
```

---

## P2-4: `data-architecture.md`

```markdown
# Data Architecture

## Cross-System Data Flow

\```mermaid
flowchart LR
    SvcA[(Service A DB)] -->|sync| SvcB[Service B]
    SvcA -->|event: order.created| Queue[Message Broker]
    Queue -->|consume| SvcC[Service C]
    SvcC -->|ETL| DW[(Data Warehouse)]
\```

## Data Ownership Map

| Entity | Source-of-Truth Service | Consumed By |
|--------|----------------------|-------------|
| Order | order-service | payment-service, analytics-pipeline |

## Unified Data Dictionary

| Entity | Service | Field Count | PII Fields | Storage |
|--------|---------|------------|-----------|---------|
| ... | ... | ... | ... | ... |

## PII / Sensitive Data Inventory

| Service | Entity | Field | Classification | Protection |
|---------|--------|-------|---------------|-----------|
| user-service | User | email | PII | encrypted-at-rest |

## Database Technology Landscape

| Technology | Version | Services Using |
|-----------|---------|---------------|
| PostgreSQL | 15 | service-a, service-b |
```

---

## P2-5: `event-topology.md`

```markdown
# Event & Message Topology

## Flow Map

\```mermaid
flowchart LR
    subgraph Publishers
        ...
    end
    subgraph Broker["Broker"]
        ...
    end
    subgraph Subscribers
        ...
    end
\```

## Topic/Queue Registry

| Topic/Queue | Broker | Publisher(s) | Subscriber(s) | Schema |
|------------|--------|-------------|---------------|--------|
| order.created | Kafka | order-service | payment-service, notification-service | OrderCreatedEvent |

## Async Pattern Inventory

| Pattern | Where Used | Description |
|---------|-----------|-------------|
| Pub/Sub | order.created | Order events broadcast to multiple consumers |
| Saga | order-payment flow | Choreography-based distributed transaction |

## Orphan Detection

| Issue | Topic | Details |
|-------|-------|---------|
| No subscribers | unused.topic | Published by service-x, no consumers found |
```

---

## P2-6: `dependency-graph.md`

```markdown
# Dependency Graph & Blast Radius Analysis

## Service Dependency Graph

\```mermaid
flowchart TD
    ServiceA --> ServiceB
    ServiceA --> ServiceC
    ServiceB --> ServiceD
    ServiceC --> ServiceD
\```

## Blast Radius Analysis

| Service | Direct Dependents | Transitive Dependents | Blast Radius Score |
|---------|------------------|----------------------|-------------------|
| service-d | service-b, service-c | service-a | 3 (critical) |

## Circular Dependencies

| Cycle | Services Involved | Severity |
|-------|------------------|----------|
| Cycle 1 | A → B → C → A | High |

## Static vs. Runtime Dependency Diff

| Source | Target | Static (P1-4) | Runtime (P1-13) | Status |
|--------|--------|--------------|----------------|--------|
| svc-a | svc-b | declared | observed | Confirmed |
| svc-a | svc-c | declared | not observed | Possibly stale |
| svc-b | svc-d | not declared | observed | Undocumented |

## External Dependency Risk

| External Service | Services Depending | Criticality |
|-----------------|-------------------|-------------|
| Stripe | payment-svc, billing-svc | Critical |
```

---

## P2-7: `infrastructure-map.md`

```markdown
# Infrastructure & Deployment Map

## Deployment Topology

\```mermaid
flowchart TD
    subgraph Prod["Production"]
        subgraph RegionA["Region A"]
            ...
        end
    end
\```

## Environment Summary

| Environment | Services | Cloud Provider | Regions |
|------------|----------|---------------|---------|
| prod | N | Azure | East US, West EU |

## Cloud Resource Inventory

| Resource Type | Count | Services | Est. Monthly Cost |
|--------------|-------|---------|-------------------|
| AKS Cluster | 2 | all services | ... |

## Cost Allocation by Team

| Team | Resources | Services | Est. Monthly Cost |
|------|----------|---------|-------------------|
| ... | ... | ... | ... |
```

---

## P2-8: `security-posture.md`

```markdown
# Security Posture

## Authentication Landscape

| Mechanism | Services Using | Notes |
|-----------|---------------|-------|
| OAuth2 | service-a, service-b | via Azure AD |

## Authorization Models

| Model | Services Using | Details |
|-------|---------------|---------|
| RBAC | service-a | 5 roles defined |

## Compliance Coverage Matrix

| Repo | GDPR | SOC2 | HIPAA | PCI-DSS |
|------|------|------|-------|---------|
| service-a | ✓ | ✓ | - | - |

## Data Classification Inventory

| Classification | Total Fields | Services | Example Fields |
|---------------|-------------|---------|---------------|
| PII | N | service-a, service-b | email, phone |

## Gap Analysis

| Issue | Severity | Affected Services | Recommendation |
|-------|----------|------------------|---------------|
| No auth on internal API | Medium | service-x | Add mTLS |
```

---

## P2-9: `operational-handbook.md`

```markdown
# Operational Handbook

## SLO Summary

| Service | SLI | Target | Current Baseline |
|---------|-----|--------|-----------------|
| order-service | Availability | 99.9% | 99.95% |

## Monitoring Dashboard Index

| Service | Dashboard Link | Key Metrics |
|---------|---------------|-------------|
| ... | ... | ... |

## System-Wide Incident Playbook

### Scenario: {Database Outage}
**Affected Services**: ...
**Detection**: ...
**Mitigation Steps**:
1. ...

## Runtime Health Baselines

| Service | Normal p50 | Normal p95 | Normal Error Rate |
|---------|-----------|-----------|------------------|
| ... | Xms | Xms | X% |

## Runbook Index

| Service | Runbook | Key Procedures |
|---------|---------|---------------|
| [order-service](../phase1/order-service/runbook.md) | Restart, scaling, backup |
```

---

## P2-10: `domain-map.md`

```markdown
# Business Domain Map

## Domain-to-Service Matrix

| Domain | Services | Frontends | Pipelines |
|--------|---------|-----------|----------|
| Orders | order-service, order-worker | customer-portal | order-analytics |

## Business Capability Model

\```mermaid
flowchart TD
    subgraph Core["Core Capabilities"]
        ...
    end
    subgraph Supporting["Supporting"]
        ...
    end
    subgraph Generic["Generic"]
        ...
    end
\```

## Cross-Domain Business Processes

### {Process: Order Fulfillment}

\```mermaid
sequenceDiagram
    Customer->>OrderDomain: Place order
    OrderDomain->>PaymentDomain: Process payment
    PaymentDomain-->>OrderDomain: Payment confirmed
    OrderDomain->>FulfillmentDomain: Ship order
\```
```

---

## P2-11: `onboarding-guide.md`

```markdown
# Developer Onboarding Guide

## System Overview

Brief narrative describing the system, its purpose, and its architecture.
Reference: [System Architecture](./system-architecture.md)

## Finding Your Way

- **Service Catalog**: [service-catalog.md](./service-catalog.md) — Find any repo by team, tech stack, or domain
- **Architecture**: [system-architecture.md](./system-architecture.md) — Understand how services connect
- **APIs**: [api-catalog.md](./api-catalog.md) — Discover available APIs

## Development Environment Setup

### Prerequisites
- ...

### Common Tools
| Tool | Version | Purpose |
|------|---------|---------|
| ... | ... | ... |

## Repository Types & Conventions

| Type | Example Repo | Key Conventions |
|------|-------------|----------------|
| Backend Service | order-service | ... |
| Frontend | customer-portal | ... |

## Contribution Workflow

1. ...
2. ...
```

---

## P2-12: `tech-landscape.md`

```markdown
# Technology Landscape & Health

## Stack Inventory

| Category | Technology | Version(s) | Repo Count | Standard? |
|----------|----------|-----------|-----------|----------|
| Language | TypeScript | 5.x | 15 | Yes |
| Language | C# | 12 | 10 | Yes |

## Library Version Currency

| Library | Latest | Versions in Use | Repos Outdated |
|---------|--------|----------------|---------------|
| express | 5.0 | 4.18 (8), 5.0 (4) | 8 |

## Standardization Analysis

| Area | Standard | Adoption | Divergent Repos |
|------|---------|----------|----------------|
| HTTP Framework | Express | 80% | service-x (Fastify), service-y (Koa) |

## Modernization Opportunity Matrix

| Repo | Current Stack | Issue | Opportunity | Effort |
|------|-------------|-------|------------|--------|
| legacy-svc | Node 14, Express 4 | EOL runtime | Upgrade to Node 20 | Medium |
```

---

## P2-13: `system-runtime-profile.md`

```markdown
# System Runtime Profile

## System-Wide Latency Heatmap

| Caller ↓ / Callee → | service-a | service-b | service-c |
|---------------------|-----------|-----------|-----------|
| **frontend** | 45ms | - | 120ms |
| **service-a** | - | 12ms | 89ms |

## Traffic Flow

\```mermaid
flowchart TD
    Frontend -->|"50K req/day"| ServiceA
    ServiceA -->|"30K req/day"| ServiceB
    ServiceA -->|"20K req/day"| ServiceC
    ServiceB -->|"15K req/day"| DB[(Database)]
\```

## Bottleneck Analysis

| Service | p99 Latency | Error Rate | CPU Peak | Assessment |
|---------|------------|-----------|----------|-----------|
| service-c | 500ms | 2.1% | 85% | Bottleneck — needs optimization |

## Error Hotspot Map

| Service | Error Rate | Top Error | Category |
|---------|-----------|-----------|----------|
| ... | ... | ... | ... |

## Critical Business Paths

### {Path: Customer Purchase}

\```mermaid
sequenceDiagram
    Frontend->>OrderSvc: POST /orders (45ms)
    OrderSvc->>PaymentSvc: POST /payments (120ms)
    PaymentSvc-->>OrderSvc: 200 OK (80ms)
    OrderSvc->>NotifSvc: event: order.confirmed (async)
\```

**Total p95 latency**: Xms
**Critical bottleneck**: PaymentSvc (contributes X% of total latency)
```
