---
description: "Use when generating YAML documentation artifacts for the multi-repo system documentation framework. Covers repo-identity.yaml, dependencies.yaml, events.yaml, configuration.yaml, deployment.yaml, security.yaml, operations.yaml, pipeline.yaml, runtime-behavior.yaml, api-summary.yaml, and all Phase 2 YAML outputs."
applyTo: "architects-metadata/**/*.yaml"
---

# YAML Artifact Format Standards

## Dual-Format Rule

Every artifact MUST be produced in two formats:
1. **YAML** (`.yaml`) — Machine-parseable, schema-validated, used by Phase 2 aggregation
2. **Markdown** (`.md`) — Human-readable narrative with Mermaid diagrams where applicable

The YAML file is the source of truth. The Markdown file is a rendered view with additional narrative context.

## YAML Conventions

### Key Naming
- Use `kebab-case` for all keys: `repo-name`, `tech-stack`, `api-endpoints`
- Do NOT use `camelCase`, `snake_case`, or `PascalCase`

### Required Header
Every YAML artifact MUST start with these metadata fields:

```yaml
artifact: <artifact-code>    # e.g., "P1-1", "P1-4", "P2-2"
version: "1.0"
generated: <ISO-8601 date>   # e.g., "2026-03-30"
repo-name: <repo-name>       # Phase 1 only
```

### Value Rules
- **NEVER** include secret values, passwords, tokens, or connection strings
- **NEVER** include environment-specific values (URLs, IPs, ports for specific environments)
- For secrets: use the format `name: "DB_PASSWORD"` with `sensitivity: secret` — never the value
- For env vars: document `name`, `purpose`, and `sensitivity` — never the value
- Use `null` for unknown/unresolved values, not empty strings
- Use ISO-8601 for all dates
- Use arrays for multi-value fields, even if currently single-valued

### Data Classification Tags
When documenting data fields, always include classification:

```yaml
classification: public | internal | sensitive | pii
```

### Cross-References
When referencing other repos or artifacts, use the standard reference format:

```yaml
depends-on:
  - repo: "repo-name"
    artifact: "P1-4"
    field: "produced-apis"
```

## Validation

Before finalizing any YAML artifact:
1. Confirm the `artifact` header matches the expected code (P1-1 through P1-13, P2-1 through P2-13)
2. Confirm all keys use `kebab-case`
3. Confirm no secret values are present
4. Confirm the file is valid YAML (no tabs, proper indentation with 2 spaces)
5. Confirm arrays are used for multi-value fields
