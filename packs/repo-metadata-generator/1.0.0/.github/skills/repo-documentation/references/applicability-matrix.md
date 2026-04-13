# Applicability Matrix — Phase 1 Artifacts by Repo Type

Use this matrix to determine which Phase 1 artifacts apply to each repository type.

## Decision Matrix

| Phase 1 Artifact | Code | Services | Frontends | Libraries | IaC | Pipelines |
|---|---|---|---|---|---|---|
| Identity Card | P1-1 | **Required** | **Required** | **Required** | **Required** | **Required** |
| API Contracts | P1-2 | **Required** | If BFF | Skip | Skip | Skip |
| Data Model | P1-3 | **Required** | Skip | Skip | Skip | **Required** |
| Dependency Manifest | P1-4 | **Required** | **Required** | **Required** | **Required** | **Required** |
| Event Catalog | P1-5 | **Required** | Skip | Skip | Skip | **Required** |
| Configuration | P1-6 | **Required** | **Required** | **Required** | **Required** | **Required** |
| Deployment | P1-7 | **Required** | **Required** | Skip | **Required** | **Required** |
| Architecture | P1-8 | **Required** | **Required** | Skip | Skip | Skip |
| Security Profile | P1-9 | **Required** | **Required** | **Required** | **Required** | **Required** |
| Business Domain | P1-10 | **Required** | **Required** | Skip | Skip | **Required** |
| Ops Profile | P1-11 | **Required** | **Required** | Skip | Skip | **Required** |
| Pipeline Spec | P1-12 | Skip | Skip | Skip | Skip | **Required** |
| Runtime Behavior | P1-13 | **Required** | **Required** | Skip | Skip | **Required** |

## Repo Type Detection Rules

### Service
A repo is a **service** if it has ANY of:
- API controllers/routes (Express routes, ASP.NET controllers, Spring controllers, FastAPI routers)
- A `Dockerfile` or `docker-compose.yml` with an application service
- Database migration files
- Health check endpoints
- gRPC proto definitions used as a server

### Frontend
A repo is a **frontend** if it has ANY of:
- `package.json` with React, Angular, Vue, Svelte, or Next.js
- HTML templates or static site generators
- SPA routing configuration
- Static asset build pipeline (webpack, vite, esbuild)

**BFF detection**: A frontend has a Backend-For-Frontend (BFF) layer if it contains server-side API routes (e.g., Next.js API routes, Nuxt server routes).

### Library
A repo is a **library** if it has ALL of:
- A package publishing configuration (npm `package.json` with `main`/`exports`, NuGet `.csproj` with `<IsPackable>true</IsPackable>`, PyPI `setup.py`/`pyproject.toml`)
- No standalone deployment configuration (no Dockerfile for running, no k8s manifests, no CI/CD deploy stages)
- Consumed by other repos as a dependency

### IaC (Infrastructure as Code)
A repo is **IaC** if its PRIMARY content is:
- Terraform files (`.tf`, `.tfvars`)
- Bicep files (`.bicep`)
- ARM templates (`.json` with `$schema` referencing `deploymentTemplate`)
- Pulumi programs
- CloudFormation templates
- Ansible playbooks

### Pipeline (Data Pipeline)
A repo is a **pipeline** if it has ANY of:
- ETL/ELT scripts or configurations
- Airflow DAGs
- Azure Data Factory pipelines
- Spark/Databricks jobs
- Data transformation scripts that read from sources and write to sinks

## Edge Cases

- **Monorepo with multiple types**: Treat each deployable unit as a separate "repo" for documentation purposes. Create one Phase 1 folder per unit.
- **Service + IaC in same repo**: Classify as `service` (primary function). IaC-specific artifacts (infrastructure inventory) are captured in P1-7 Deployment.
- **Frontend + BFF**: Classify as `frontend`. Generate P1-2 API Contracts for the BFF endpoints.
- **Library that also has a CLI**: Classify as `library` if the primary purpose is consumption as a dependency. If the CLI is the primary artifact, classify as `service`.
