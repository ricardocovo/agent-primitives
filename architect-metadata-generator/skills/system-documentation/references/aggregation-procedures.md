# Aggregation Procedures — Phase 2 Synthesis Logic

Detailed procedures for synthesizing Phase 1 per-repository artifacts into Phase 2 system-level documents.

---

## P2-1: System Architecture Overview

### Input
All `repo-identity.yaml` (P1-1), `dependencies.yaml` (P1-4), `architecture.md` (P1-8), `domain.md` (P1-10)

### Procedure
1. **Build service inventory** from P1-1: list all repos by type (service, frontend, library, IaC, pipeline)
2. **Build relationship graph** from P1-4: for each repo, collect `internal-dependencies` (direction: calls/called-by)
3. **Identify bounded contexts** from P1-10: group repos by their stated bounded context
4. **Create layered view**: organize repos into layers (frontend → backend services → data stores → infrastructure)
5. **Generate Mermaid diagrams**:
   - High-level system architecture (all services, grouped by domain)
   - Technology landscape (grouped by language/framework)
   - Bounded context map (DDD)
6. **Write narrative**: summarize tech stack choices, key architectural patterns, and design philosophy

---

## P2-2: Service Catalog

### Input
All `repo-identity.yaml` (P1-1)

### Procedure
1. **Collect** all `repo-identity.yaml` files
2. **Build registry table** with columns: repo-name, description, team, type, status, languages, frameworks
3. **Group by team**: show each team's owned repos
4. **Group by type**: services, frontends, libraries, IaC, pipelines
5. **Group by domain**: use bounded context from P1-10 if available
6. **Generate YAML catalog** (`service-catalog.yaml`): structured array of all repos for machine consumption
7. **Generate quick-reference cards**: for each service, a compact summary block in Markdown

---

## P2-3: API Catalog

### Input
All `api/api-summary.yaml` (P1-2)

### Procedure
1. **Collect** all `api-summary.yaml` files across repos
2. **Build unified endpoint list**: all endpoints across all repos
3. **Build dependency matrix**: cross-reference with P1-4 `consumed-apis` to show which repo calls which endpoint
4. **Flag issues**: deprecated endpoints still consumed, un-authenticated endpoints, inconsistent versioning
5. **Generate diagrams**: API dependency flow (which frontend calls which backend endpoint)
6. **Produce dual format**: `api-catalog.yaml` (structured) + `api-catalog.md` (human-readable with tables)

---

## P2-4: Data Architecture

### Input
All `data-model/schema.yaml` (P1-3), `dependencies.yaml` (P1-4), `events.yaml` (P1-5), `pipeline.yaml` (P1-12)

### Procedure
1. **Build entity inventory**: collect all entities from all P1-3 files, noting which repo "owns" each
2. **Identify shared entities**: entities with the same or similar name across repos → flag for ownership analysis
3. **Build data flow diagram**: 
   - Sync flows: from P1-4 consumed/produced APIs (data exchanged via APIs)
   - Async flows: from P1-5 (data exchanged via events)
   - ETL flows: from P1-12 (data pipeline source → sink)
4. **Build data ownership map**: for each entity, determine source-of-truth repo
5. **Aggregate PII inventory**: collect all fields with `classification: pii` across all repos
6. **Database landscape**: list all database technologies, versions, and which repos use them
7. **Generate Mermaid**: cross-system data flow diagram with service nodes and data arrows

---

## P2-5: Event & Message Topology

### Input
All `events.yaml` (P1-5)

### Procedure
1. **Collect** all `events.yaml` files
2. **Build flow map**: for each topic/queue, list publishers and subscribers
3. **Detect anomalies**:
   - Orphaned topics: published but no consumer
   - Unmatched consumers: consuming from topic with no declared publisher
   - Single-consumer topics: potential for direct API call instead
4. **Group by broker**: separate flows by messaging technology
5. **Generate Mermaid**: publisher → topic → subscriber flow diagram
6. **Produce dual format**: `event-topology.yaml` (structured) + `event-topology.md` (visual)

---

## P2-6: Dependency Graph & Blast Radius

### Input
All `dependencies.yaml` (P1-4), `runtime-behavior.yaml` (P1-13)

### Procedure
1. **Build adjacency list** from P1-4: for each repo, list direct dependencies (calls) and reverse dependencies (called-by)
2. **Detect cycles**: use DFS traversal to find circular dependency chains
3. **Compute blast radius**: for each service, follow reverse dependencies to calculate how many services are affected if it fails
4. **Rank by criticality**: services with highest blast radius first
5. **Static vs. Runtime diff** (if P1-13 available):
   - Declared dependencies (P1-4) not observed in traces → possibly stale
   - Runtime dependencies (P1-13 trace-dependencies) not declared → undocumented
6. **External dependency risk**: aggregate external dependencies from all P1-4 files, rank by criticality
7. **Generate Mermaid**: full dependency graph with color-coded blast radius

---

## P2-7: Infrastructure & Deployment Map

### Input
All `deployment.yaml` (P1-7)

### Procedure
1. **Build deployment topology** per environment (dev, staging, prod)
2. **Aggregate cloud resources**: all provisioned resources across all repos
3. **Map network topology**: which services communicate and in which network segments
4. **Cost allocation draft**: group resources by team/service (from P1-1 owner + P1-7 resources)
5. **Generate Mermaid**: deployment topology diagrams per environment

---

## P2-8: Security Posture

### Input
All `security.yaml` (P1-9), `api/api-summary.yaml` (P1-2), `data-model/schema.yaml` (P1-3)

### Procedure
1. **Build auth flow diagram**: map authentication mechanisms across services, trace token flow
2. **Data classification inventory**: aggregate all data fields with classification from P1-3 across the system
3. **Compliance matrix**: for each compliance tag (GDPR, SOC2, etc.), list which repos claim compliance
4. **Gap analysis**: identify repos missing security profiles, endpoints without auth, PII without encryption
5. **Attack surface summary**: external-facing endpoints, third-party integrations, data exposure points

---

## P2-9: Operational Handbook

### Input
All `operations.yaml` (P1-11), `runbook.md` (P1-11), `runtime-behavior.yaml` (P1-13)

### Procedure
1. **Index runbooks** by service name (from P1-11 runbooks)
2. **Build SLO/SLA summary**: aggregate SLIs and targets from all P1-11 files
3. **Define runtime baselines** from P1-13: normal ranges for latency, error rates, and throughput per service
4. **Aggregate alerting rules**: all alerts from all services, grouped by severity
5. **Build incident response playbook**: common cross-service failure scenarios and system-wide remediation steps
6. **Dashboard inventory**: list all monitoring dashboards (from P1-1 links + P1-11)

---

## P2-10: Business Domain Map

### Input
All `domain.md` (P1-10)

### Procedure
1. **Build domain inventory**: list all bounded contexts and their owning repos
2. **Create domain-to-service matrix**: which services belong to which domain
3. **Map domain dependencies**: upstream/downstream relationships between bounded contexts
4. **Visualize business capability model**: organize domains by business capability hierarchy
5. **Generate Mermaid**: domain map showing context boundaries and relationships

---

## P2-11: Developer Onboarding Guide

### Input
All `repo-identity.yaml` (P1-1), `architecture.md` (P1-8), plus P2-1 and P2-2 (already generated)

### Procedure
1. **Write system overview narrative**: synthesize from P2-1, explain the big picture in 2-3 paragraphs
2. **Development environment setup**: aggregate common tools and tech stacks from P1-1, provide setup instructions
3. **Navigation guide**: explain how repos are organized, how to find services by domain/team, where docs live
4. **Contribution workflow**: describe the standard PR/review/deploy flow (reference P1-7 CI/CD patterns)
5. **Link to deeper resources**: point to P2-2 (Service Catalog), P2-1 (Architecture), per-repo docs

---

## P2-12: Technology Landscape & Health

### Input
All `repo-identity.yaml` (P1-1), `dependencies.yaml` (P1-4), `deployment.yaml` (P1-7)

### Procedure
1. **Technology inventory**: aggregate all languages, frameworks, databases, cloud services, tools
2. **Standardization analysis**: identify where stacks diverge (e.g., 3 repos on Express, 2 on Fastify, 1 on Koa)
3. **Library version currency**: from P1-4 key-libraries, flag outdated versions
4. **Technical debt inventory**: compile known debt items (deprecated frameworks, EOL runtimes, legacy patterns)
5. **Modernization opportunity matrix**: recommend upgrades or consolidation opportunities

---

## P2-13: System Runtime Profile

### Input
All `runtime-behavior.yaml` (P1-13)

### Procedure
1. **Build latency heatmap**: matrix of service-to-service call latencies (p50, p95, p99)
2. **Traffic flow visualization**: actual request volumes between services (from P1-13 call-patterns)
3. **Bottleneck analysis**: identify services with highest latency or error rates
4. **Error hotspot map**: aggregate error rates and top errors across all services
5. **Peak load patterns**: identify time-based traffic patterns and capacity planning data
6. **Critical path analysis**: trace end-to-end latency for key business transactions
7. **Generate Mermaid**: system-wide call flow diagram with volume and latency annotations
