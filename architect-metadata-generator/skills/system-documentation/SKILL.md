---
name: system-documentation
description: "Synthesize Phase 1 per-repository artifacts into system-level Phase 2 documentation. Use when aggregating repo-identity, dependencies, API contracts, data models, events, deployment, security, operations, architecture, domain context, and runtime behavior across all repositories into system-wide views like service catalog, dependency graph, API catalog, data architecture, and operational handbook."
argument-hint: "Path to phase1/ folder containing per-repo artifacts"
---

# System-Level Documentation (Phase 2)

Synthesize per-repository Phase 1 artifacts into comprehensive system-level documents for the multi-repo documentation framework.

## When to Use

- All or most Phase 1 artifacts are available across repositories
- After completing a documentation wave and ready to aggregate
- Refreshing system-level views after Phase 1 updates
- Generating specific system-level documents (can run individually)

## Prerequisites

- Phase 1 artifacts exist in `architects-metadata/phase1/` for the target repos
- At minimum, P1-1 (Identity) and P1-4 (Dependencies) should exist for all repos (Wave 1 complete)

## Procedure

### Step 1 — Inventory Phase 1 Outputs

1. Scan `architects-metadata/phase1/` for all repo folders
2. For each folder, list which artifacts exist
3. Produce a completeness report:

```
Repo: order-service     Type: service     Artifacts: 13/13 ✓
Repo: payment-api       Type: service     Artifacts: 11/13 (missing: P1-12, P1-13)
Repo: shared-ui         Type: library     Artifacts: 6/6 ✓
```

### Step 2 — Validate Minimum Requirements

Check the [source mapping](./references/source-mapping.md) for each P2 document to confirm its required P1 sources exist.

| P2 Document | Can Generate After | Minimum P1 Sources |
|---|---|---|
| P2-2 Service Catalog | Wave 1 | P1-1 from all repos |
| P2-6 Dependency Graph | Wave 1 | P1-4 from all repos |
| P2-1 System Architecture | Wave 1 + partial Wave 5 | P1-1, P1-4, P1-8 (partial OK) |
| P2-3 API Catalog | Wave 2 | P1-2 from API-bearing repos |
| P2-5 Event Topology | Wave 3 | P1-5 from event-using repos |
| P2-4 Data Architecture | Wave 3 | P1-3, P1-4, P1-5 |
| P2-7 Infrastructure Map | Wave 4 | P1-7 from all deployable repos |
| P2-8 Security Posture | Wave 4 | P1-9, P1-2, P1-3 |
| P2-9 Ops Handbook | Wave 4 | P1-11 from all prod services |
| P2-10 Domain Map | Wave 5 | P1-10 from services/frontends/pipelines |
| P2-11 Onboarding Guide | After P2-1, P2-2 | P1-1, P1-8 + P2-1, P2-2 |
| P2-12 Tech Landscape | Wave 1 + Wave 4 | P1-1, P1-4, P1-7 |
| P2-13 Runtime Profile | Wave 2 | P1-13 from all prod services |

### Step 3 — Generate Documents

For each P2 document:
1. Consult [aggregation procedures](./references/aggregation-procedures.md) for synthesis logic
2. Consult [output templates](./references/output-templates.md) for structure
3. Read all required P1 artifacts across repos
4. Aggregate, cross-reference, and synthesize
5. Generate Mermaid diagrams for visual views
6. Write to `architects-metadata/phase2/`

### Step 4 — Cross-Reference

After generating documents:
- Verify all cross-references between P2 documents are valid
- Verify all references back to P1 sources point to existing files
- Ensure consistent terminology (repo names, entity names, service names)

### Step 5 — Output Summary

List all generated P2 documents with:
- Document name and path
- Number of repos contributing data
- Completeness assessment (full / partial — citing missing P1 data)
- Key findings or highlights
