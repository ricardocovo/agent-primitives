---
description: "Generate P1-4 dependencies.yaml — dependency manifest listing all upstream and downstream service dependencies, package dependencies, infrastructure dependencies, and shared library references. Wave 1 artifact — generate this early."
tools: [read, search, execute]
---

Generate **dependencies.yaml** (P1-4) for the repository at `$input`.

## What to Analyze

1. **Package dependencies**: package.json, pom.xml, build.gradle, *.csproj, requirements.txt, go.mod, Cargo.toml, Gemfile
2. **Service dependencies**: HTTP client calls, gRPC client stubs, service discovery configs, API gateway routes, connection strings to other services
3. **Infrastructure dependencies**: Database connections, message broker configs (Kafka, RabbitMQ, Service Bus), cache (Redis), blob storage, external APIs
4. **Shared library references**: Internal packages, NuGet/npm/Maven internal feeds, git submodules
5. **Build-time dependencies**: CI/CD tool references, Docker base images, build plugins

## Output

Write to `architects-metadata/phase1/{repo-name}/dependencies.yaml`

```yaml
artifact: dependencies
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

upstream-services:
  - name: "{service-name}"
    protocol: "{HTTP|gRPC|message|database}"
    purpose: "{why this dependency exists}"
    criticality: "{critical|degraded|optional}"

downstream-consumers:
  - name: "{service-name or 'unknown'}"
    protocol: "{HTTP|gRPC|message}"
    notes: "{how discovered}"

infrastructure:
  - type: "{database|cache|message-broker|blob-storage|search|external-api}"
    technology: "{PostgreSQL|Redis|Kafka|etc}"
    purpose: "{what it's used for}"

packages:
  runtime:
    - name: "{package}"
      version: "{version or range}"
      scope: "{production dependency}"
  dev:
    - name: "{package}"
      version: "{version}"
      scope: "{development only}"

shared-libraries:
  - name: "{internal package name}"
    source: "{feed or repo URL}"
    version: "{version}"

build-dependencies:
  docker-base-image: "{image:tag}"
  ci-tools: ["{tool1}", "{tool2}"]
```

## Validation

- All connection strings and HTTP client configurations found in code must map to an entry
- `criticality` must be assessed (critical = service fails without it, degraded = partial functionality, optional = graceful fallback)
- No secret values in the output — only names, purposes, and technologies
