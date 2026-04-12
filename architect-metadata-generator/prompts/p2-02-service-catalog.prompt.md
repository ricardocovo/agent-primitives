---
description: "Synthesize P2-2 Service Catalog from P1-1 across all repos — produces a system-wide registry of all services, libraries, and components with ownership, status, tech stack, and classification. Can start after Wave 1."
tools: [read, search, edit]
---

Synthesize a **Service Catalog** (P2-2) from Phase 1 artifacts.

## Prerequisites

Requires from `architects-metadata/phase1/`:
- **P1-1 repo-identity.yaml** from all repos

## Synthesis Procedure

1. **Read all P1-1 files** → Extract: repo-name, display-name, repo-type, owner, lifecycle-status, tech-stack, description
2. **Group by type** → Organize repos into services, frontends, libraries, IaC, pipelines
3. **Group by team** → Show ownership distribution
4. **Identify patterns** → Common tech stacks, lifecycle status distribution, gaps

## Output

Write to `architects-metadata/phase2/service-catalog.yaml` and `service-catalog.md`

### YAML Structure

```yaml
artifact: service-catalog
version: "1.0"
generated: "{ISO-8601 date}"
total-repos: "{count}"

services:
  - repo-name: "{name}"
    display-name: "{name}"
    repo-type: "{type}"
    owner: "{team}"
    lifecycle-status: "{status}"
    primary-language: "{language}"
    description: "{purpose}"
```

### Markdown Sections

1. **Catalog Summary** — Total counts by type, by team, by status
2. **Services Table** — Full table of all service repos
3. **Frontends Table** — All frontend repos
4. **Libraries Table** — All shared libraries
5. **Infrastructure & Pipelines** — IaC and pipeline repos
6. **Ownership Distribution** — Mermaid pie chart or bar showing repos per team
7. **Technology Distribution** — Languages and frameworks across the system
8. **Lifecycle Status** — Active vs. maintenance vs. deprecated breakdown
9. **Gaps & Recommendations** — Repos missing owners, deprecated repos still in use, etc.

## Validation

- Every repo with a P1-1 file must appear in the catalog
- Count totals must match actual files found
- No duplicate entries
