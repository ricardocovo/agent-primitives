---
description: "Generate P1-6 configuration.yaml — configuration documentation listing all environment variables, feature flags, config files, and secrets references with descriptions, defaults, and sensitivity levels. Wave 4 artifact."
tools: [read, search, execute]
---

Generate **configuration.yaml** (P1-6) for the repository at `$input`.

## What to Analyze

1. **Environment variables**: `.env` files, `docker-compose.yml` env sections, Kubernetes ConfigMaps/Secrets, `process.env.*` / `Environment.GetEnvironmentVariable()` / `os.getenv()` references in code
2. **Configuration files**: `appsettings.json`, `application.yml`, `config.yaml`, `.ini` files, `web.config`
3. **Feature flags**: LaunchDarkly, Azure App Configuration, custom feature flag implementations
4. **Secrets references**: Key Vault references, AWS Secrets Manager, HashiCorp Vault, `secrets.*` patterns (document name and purpose ONLY, never values)
5. **Default values**: Hardcoded defaults in code, fallback values in config loading
6. **Per-environment overrides**: How config differs across dev/staging/production

## Output

Write to `architects-metadata/phase1/{repo-name}/configuration.yaml`

```yaml
artifact: configuration
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

config-sources:
  - type: "{env-var|config-file|key-vault|feature-flag|app-config}"
    location: "{file path or service name}"

environment-variables:
  - name: "{VAR_NAME}"
    description: "{purpose}"
    default: "{default value or null}"
    required: true|false
    sensitive: true|false
    example: "{non-sensitive example}"

feature-flags:
  - name: "{flag-name}"
    description: "{what it controls}"
    default: true|false
    provider: "{LaunchDarkly|AppConfig|custom}"

secrets:
  - name: "{secret-name}"
    purpose: "{what it's used for}"
    source: "{Key Vault|Secrets Manager|env-var}"
    rotation-policy: "{description or unknown}"

per-environment:
  - environment: "{dev|staging|production}"
    overrides: ["{list of vars that differ}"]
```

## Validation

- Every `process.env.*` / config access in code must be documented
- Sensitive values must be marked `sensitive: true`
- NEVER include actual secret values — only names and purposes
