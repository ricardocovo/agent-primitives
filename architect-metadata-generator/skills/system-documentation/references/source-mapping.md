# Source Mapping — Phase 1 to Phase 2 Dependencies

Maps each Phase 2 system-level document to its required Phase 1 source artifacts, with transformation rules.

---

## Dependency Matrix

| Phase 2 Output | P1-1 | P1-2 | P1-3 | P1-4 | P1-5 | P1-6 | P1-7 | P1-8 | P1-9 | P1-10 | P1-11 | P1-12 | P1-13 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **P2-1** System Architecture | ● | | | ● | | | | ● | | ● | | | |
| **P2-2** Service Catalog | ● | | | | | | | | | | | | |
| **P2-3** API Catalog | | ● | | | | | | | | | | | |
| **P2-4** Data Architecture | | | ● | ● | ● | | | | | | | ● | |
| **P2-5** Event Topology | | | | | ● | | | | | | | | |
| **P2-6** Dependency Graph | | | | ● | | | | | | | | | ● |
| **P2-7** Infrastructure Map | | | | | | | ● | | | | | | |
| **P2-8** Security Posture | | ● | ● | | | | | | ● | | | | |
| **P2-9** Ops Handbook | | | | | | | | | | | ● | | ● |
| **P2-10** Domain Map | | | | | | | | | | ● | | | |
| **P2-11** Onboarding Guide | ● | | | | | | | ● | | | | | |
| **P2-12** Tech Landscape | ● | | | ● | | | ● | | | | | | |
| **P2-13** Runtime Profile | | | | | | | | | | | | | ● |

● = Required source

---

## Transformation Rules Per P2 Document

### P2-1: System Architecture
- **P1-1** → Extract repo names, types, and tech stacks for landscape overview
- **P1-4** → Build service relationship graph (internal-dependencies + consumed/produced APIs)
- **P1-8** → Import per-repo component diagrams, combine into system view
- **P1-10** → Map bounded contexts, create domain boundary subgraphs

### P2-2: Service Catalog
- **P1-1** → Direct aggregation: collect all `repo-identity.yaml` files into a registry
- **Transform**: Sort by team, group by repo-type, tag by status

### P2-3: API Catalog
- **P1-2** → Merge all `api-summary.yaml` endpoints into unified inventory
- **Transform**: Build inter-service API dependency matrix (cross-ref with P1-4 consumed-apis), flag deprecated endpoints, group by auth requirements

### P2-4: Data Architecture
- **P1-3** → Merge entity definitions across repos, identify shared entities
- **P1-4** → Map data flow between services (which service produces/consumes which data)
- **P1-5** → Add event-carried data flows (events as data transport)
- **P1-12** → Add pipeline data flows (ETL source → transform → sink)
- **Transform**: Build data ownership map (source-of-truth per entity), aggregate PII inventory

### P2-5: Event Topology
- **P1-5** → Merge all `events.yaml`: collect all published-events and consumed-events
- **Transform**: Build publisher → topic/queue → subscriber flow map, detect orphaned topics (published but no consumer), detect unmatched consumers

### P2-6: Dependency Graph
- **P1-4** → Build adjacency list from all `dependencies.yaml` internal-dependencies
- **P1-13** → Overlay runtime-observed dependencies from `trace-dependencies`
- **Transform**: Detect cycles (DFS/BFS), compute blast radius (reverse dependency tree), diff static (P1-4) vs runtime (P1-13) dependencies

### P2-7: Infrastructure Map
- **P1-7** → Merge all `deployment.yaml` targets and resources
- **Transform**: Group by environment and cloud provider, build deployment topology diagram, aggregate resource inventory

### P2-8: Security Posture
- **P1-9** → Merge all `security.yaml` profiles
- **P1-2** → Cross-ref endpoints with auth requirements
- **P1-3** → Cross-ref data classification for PII exposure analysis
- **Transform**: Build end-to-end auth flow diagrams, compliance coverage matrix, identify gaps

### P2-9: Operational Handbook
- **P1-11** → Merge all `operations.yaml` and `runbook.md` files
- **P1-13** → Add runtime baselines for normal latency/error/throughput ranges
- **Transform**: Index runbooks by service, build system-wide SLO summary, aggregate alerting rules

### P2-10: Domain Map
- **P1-10** → Merge all `domain.md` bounded contexts
- **Transform**: Build domain-to-service mapping matrix, visualize upstream/downstream domain relationships, identify business capability model

### P2-11: Onboarding Guide
- **P1-1** → Repo inventory for navigation guide
- **P1-8** → Architecture summaries for system overview narrative
- **P2-1, P2-2** → Reference system architecture and service catalog (generated first)
- **Transform**: Synthesize narrative overview, create "how to find things" guide

### P2-12: Tech Landscape
- **P1-1** → Tech stacks per repo
- **P1-4** → Key libraries and their versions
- **P1-7** → Infrastructure technologies and cloud services
- **Transform**: Build technology inventory matrix, identify standardization divergences, flag outdated library versions

### P2-13: System Runtime Profile
- **P1-13** → Merge all `runtime-behavior.yaml` files
- **Transform**: Build system-wide latency heatmap (service-to-service), aggregate traffic flow volumes, identify system bottlenecks, compute end-to-end transaction latency for critical paths
