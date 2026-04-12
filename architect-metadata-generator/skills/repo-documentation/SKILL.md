---
name: repo-documentation
description: "Generate standardized per-repository documentation artifacts (Phase 1). Use when analyzing a single repository to produce identity cards, API contracts, data models, dependency manifests, event catalogs, configuration docs, deployment specs, architecture overviews, security profiles, domain context, operational profiles, pipeline specs, or runtime behavior profiles."
argument-hint: "Repository name and path to source code"
---

# Per-Repository Documentation (Phase 1)

Generate standardized documentation artifacts for a single repository as part of the multi-repo system documentation framework.

## When to Use

- Documenting a new repository joining the system
- Refreshing documentation for an existing repository after significant changes
- Running a documentation wave across multiple repos (invoke once per repo)
- Validating existing documentation completeness for a repo

## Procedure

### Step 1 — Classify the Repository Type

Analyze the repo to determine its type. This drives which artifacts are applicable.

| Repo Type | Detection Signals |
|---|---|
| **service** | Has API endpoints (controllers, routes), Dockerfile, deployment config, database migrations |
| **frontend** | Has `package.json` with React/Angular/Vue, HTML templates, static assets, SPA routing |
| **library** | Published as a package (npm, NuGet, PyPI), no deployment config, no main entry point |
| **iac** | Terraform files (`.tf`), Bicep (`.bicep`), CloudFormation, ARM templates, Pulumi |
| **pipeline** | Data processing scripts, ETL configs, Airflow DAGs, Azure Data Factory, Spark jobs |

### Step 2 — Check Applicability

Consult the [applicability matrix](./references/applicability-matrix.md) to determine which of the 13 artifacts apply to this repo type.

### Step 3 — Generate Artifacts in Wave Order

Follow the rollout wave sequence to generate artifacts in dependency order:

1. **Wave 1** (backbone): `repo-identity.yaml` (P1-1) + `dependencies.yaml` (P1-4)
2. **Wave 2** (APIs + runtime): `api/` artifacts (P1-2) + `runtime-behavior.yaml` (P1-13)
3. **Wave 3** (data + events): `data-model/` (P1-3) + `events.yaml` (P1-5) + `pipeline.yaml` (P1-12)
4. **Wave 4** (ops + security): `configuration.yaml` (P1-6) + `deployment.yaml` (P1-7) + `security.yaml` (P1-9) + `operations.yaml` (P1-11)
5. **Wave 5** (narrative): `architecture.md` (P1-8) + `domain.md` (P1-10)

Skip any artifact not applicable per Step 2.

### Step 4 — Use Templates and Schemas

For each artifact:
1. Consult [output templates](./references/output-templates.md) for the structural template
2. Consult [YAML schemas](./references/yaml-schemas.md) for schema validation requirements
3. Follow [analysis procedures](./references/analysis-procedures.md) for how to extract the data from code

### Step 5 — Write Outputs

Write all artifacts to `architects-metadata/phase1/{repo-name}/`.

### Step 6 — Validate

For each generated artifact:
- Confirm YAML is valid (2-space indent, no tabs)
- Confirm all keys use `kebab-case`
- Confirm the `artifact` header is present with the correct code
- Confirm no secret values are present
- Confirm all cross-references to other repos use the standard format

## Output Summary

After completing all artifacts, produce a brief summary listing:
- Repo name and classified type
- Which artifacts were generated (with file paths)
- Which artifacts were skipped and why (not applicable to repo type)
- Any data gaps or items needing manual review
