---
description: "Synthesize P2-3 API Catalog from P1-2 across service repos — produces a unified API registry with all endpoints, auth schemes, versioning strategies, and cross-service API dependency mapping. Requires Wave 2 P1 artifacts."
tools: [read, search, edit]
---

Synthesize an **API Catalog** (P2-3) from Phase 1 artifacts.

## Prerequisites

Requires from `architects-metadata/phase1/`:
- **P1-2 api-summary.yaml** from all service repos
- **P1-1 repo-identity.yaml** (for service identification)

## Synthesis Procedure

1. **Read all P1-2 files** → Collect every endpoint across all services
2. **Deduplicate and classify** → Group endpoints by domain, version, auth scheme
3. **Map API dependencies** → Which services call which other services' APIs (cross-ref with P1-4)
4. **Identify patterns** → Common auth schemes, versioning approaches, API style distribution
5. **Find inconsistencies** → Different auth schemes for same-tier services, missing versioning, etc.

## Output

Write to `architects-metadata/phase2/api-catalog.yaml` and `api-catalog.md`

### YAML Structure

```yaml
artifact: api-catalog
version: "1.0"
generated: "{ISO-8601 date}"
total-services: "{count}"
total-endpoints: "{count}"

services:
  - repo-name: "{name}"
    api-style: "{REST|gRPC|GraphQL|mixed}"
    base-path: "{path}"
    auth-scheme: "{scheme}"
    versioning: "{strategy}"
    endpoint-count: "{number}"
    endpoints:
      - method: "{method}"
        path: "{path}"
        summary: "{description}"
        auth-required: true|false
```

### Markdown Sections

1. **API Landscape Summary** — Total endpoints, services, API styles
2. **API Style Distribution** — REST vs. gRPC vs. GraphQL vs. message-based
3. **Endpoint Registry** — Searchable table of all endpoints by service
4. **Authentication Patterns** — Auth schemes across the system, consistency analysis
5. **Versioning Strategies** — How APIs are versioned, recommendations for alignment
6. **API Dependency Map** — Mermaid diagram showing which services consume which APIs
7. **API Gateway / BFF Layer** — If applicable, how APIs are aggregated or proxied
8. **Inconsistencies & Recommendations** — Mismatched patterns, missing docs, security gaps
