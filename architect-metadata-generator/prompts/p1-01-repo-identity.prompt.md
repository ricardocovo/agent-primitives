---
description: "Generate P1-1 repo-identity.yaml — identity card capturing repo name, owner, tech stack, repo type classification, team ownership, and lifecycle status. Wave 1 artifact — generate this first for any repo."
tools: [read, search, execute]
---

Generate a **repo-identity.yaml** (P1-1) for the repository at `$input`.

## What to Analyze

1. **Repository metadata**: README, package.json/pom.xml/csproj, CODEOWNERS, LICENSE
2. **Tech stack**: Languages (by file extension census), frameworks (from dependency files), runtime versions
3. **Repo type classification**: Determine if this is a `service`, `frontend`, `library`, `iac`, or `pipeline` repo based on:
   - `service`: Has API endpoints (controllers/routes), Dockerfile, health checks
   - `frontend`: Has React/Angular/Vue, HTML templates, SPA routing, static hosting config
   - `library`: Published as a package, no Dockerfile/deployment config
   - `iac`: Terraform/Bicep/ARM/Pulumi/CloudFormation files
   - `pipeline`: ETL scripts, Airflow DAGs, Spark jobs, data processing
4. **Ownership**: Team name from CODEOWNERS, package metadata, or org conventions
5. **Lifecycle status**: `active`, `maintenance`, `deprecated`, `archived` — infer from commit recency, README badges, deprecation notices

## Output

Write to `architects-metadata/phase1/{repo-name}/repo-identity.yaml`

```yaml
artifact: repo-identity
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

display-name: "{human-readable name}"
repo-type: "{service|frontend|library|iac|pipeline}"
description: "{one-line purpose}"
owner:
  team: "{team name}"
  contacts: ["{email or handle}"]
lifecycle-status: "{active|maintenance|deprecated|archived}"
primary-language: "{language}"
tech-stack:
  languages: ["{lang1}", "{lang2}"]
  frameworks: ["{fw1}", "{fw2}"]
  runtime: "{runtime and version}"
  build-tool: "{tool}"
repo-url: "{url}"
default-branch: "{branch}"
tags: ["{tag1}", "{tag2}"]
```

## Validation

- `repo-type` must be one of the five allowed values
- `lifecycle-status` must be one of the four allowed values
- `tech-stack.languages` must not be empty
- All required fields must be present (no missing keys)
