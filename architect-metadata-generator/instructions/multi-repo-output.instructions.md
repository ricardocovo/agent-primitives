---
description: "Use when generating documentation outputs for the multi-repo system documentation framework. Covers output folder structure, file naming conventions, and cross-reference linking for Phase 1 per-repo artifacts and Phase 2 system-level documents."
applyTo: "architects-metadata/**"
---

# Multi-Repo Documentation Output Conventions

## Folder Structure

```
architects-metadata/
├── phase1/
│   ├── {repo-name}/              # One folder per repository
│   │   ├── repo-identity.yaml
│   │   ├── api/
│   │   │   ├── api-summary.yaml
│   │   │   └── *.openapi.yaml    # Native spec files
│   │   ├── data-model/
│   │   │   ├── schema.yaml
│   │   │   └── data-model.md
│   │   ├── dependencies.yaml
│   │   ├── events.yaml
│   │   ├── configuration.yaml
│   │   ├── deployment.yaml
│   │   ├── deployment.md
│   │   ├── architecture.md
│   │   ├── security.yaml
│   │   ├── domain.md
│   │   ├── operations.yaml
│   │   ├── runbook.md
│   │   ├── pipeline.yaml          # Data pipeline repos only
│   │   ├── runtime-behavior.yaml
│   │   └── runtime-behavior.md
│   └── ...
├── phase2/
│   ├── system-architecture.md
│   ├── service-catalog.md
│   ├── service-catalog.yaml
│   ├── api-catalog.md
│   ├── api-catalog.yaml
│   ├── data-architecture.md
│   ├── event-topology.md
│   ├── event-topology.yaml
│   ├── dependency-graph.md
│   ├── infrastructure-map.md
│   ├── security-posture.md
│   ├── operational-handbook.md
│   ├── domain-map.md
│   ├── onboarding-guide.md
│   ├── tech-landscape.md
│   └── system-runtime-profile.md
└── plan-multiRepoSystemDocumentation.prompt.md  # Source plan
```

## File Naming Rules

### Phase 1 (per-repo)
- Folder name = repository name (lowercase, hyphens): `order-service`, `payment-api`
- File names are fixed per artifact type (see folder structure above)
- Do NOT prefix file names with the repo name — the folder provides that context

### Phase 2 (system-level)
- All files go directly in `phase2/` — no subfolders
- File names match the document title in kebab-case
- Dual-format artifacts get both `.md` and `.yaml` extensions

## Cross-Reference Format

When linking between artifacts within the documentation set:

### Within the same repo (Phase 1 → Phase 1)
```markdown
See [dependencies](./dependencies.yaml) for service dependencies.
See [architecture overview](./architecture.md) for component diagrams.
```

### Between repos (Phase 1 → Phase 1)
```markdown
See [payment-api dependencies](../payment-api/dependencies.yaml).
```

### Phase 2 → Phase 1
```markdown
See [order-service identity](../phase1/order-service/repo-identity.yaml).
```

### Phase 1 → Phase 2
```markdown
See [system dependency graph](../../phase2/dependency-graph.md).
```

## Repository Name Argument

All Phase 1 prompts accept the repository name as their argument. When invoked:
- The argument specifies which repo to analyze (e.g., `/p1-01-repo-identity order-service`)
- Output goes to `architects-metadata/phase1/{repo-name}/`
- The repo source code should be accessible in the workspace or at a specified path

## Completeness Tracking

Each Phase 1 repo folder should eventually contain all applicable artifacts for its repo type. Use the applicability matrix from the `repo-documentation` skill to determine which artifacts apply.
