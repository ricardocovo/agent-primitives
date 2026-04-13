---
description: "Generate P1-9 security.yaml — security profile documenting authentication mechanisms, authorization models, data protection controls, vulnerability surface, and compliance requirements. Wave 4 artifact."
tools: [read, search, execute]
---

Generate **security.yaml** (P1-9) for the repository at `$input`.

## What to Analyze

1. **Authentication**: OAuth2/OIDC config, JWT validation, API key checks, certificate-based auth, identity providers
2. **Authorization**: RBAC roles/policies, claims-based authorization, custom authorization handlers, middleware
3. **Data protection**: Encryption at rest (database encryption), encryption in transit (TLS config), field-level encryption, data masking
4. **Input validation**: Validation attributes, sanitization middleware, anti-XSS, CSRF protection
5. **Secret management**: How secrets are stored and accessed (Key Vault, Secrets Manager, env vars)
6. **Dependency vulnerabilities**: Known CVEs in dependencies (check lockfiles for old versions)
7. **CORS / headers**: CORS policy, security headers (HSTS, CSP, X-Frame-Options)
8. **Compliance markers**: PII handling, GDPR annotations, audit logging, data retention policies

## Output

Write to `architects-metadata/phase1/{repo-name}/security.yaml`

```yaml
artifact: security
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

authentication:
  mechanisms: ["{OAuth2|JWT|API-Key|Certificate|Basic|none}"]
  identity-provider: "{provider name or null}"
  token-validation: "{description}"

authorization:
  model: "{RBAC|ABAC|claims-based|custom|none}"
  roles: ["{role1}", "{role2}"]
  policies: ["{policy description}"]

data-protection:
  encryption-at-rest: true|false
  encryption-in-transit: true|false
  field-level-encryption: ["{fields if any}"]
  pii-fields: ["{fields containing PII}"]

input-validation:
  framework: "{validation library or approach}"
  csrf-protection: true|false
  xss-prevention: "{approach}"

secret-management:
  provider: "{Key-Vault|Secrets-Manager|env-vars|config-file}"
  secrets-count: "{number of secrets referenced}"

security-headers:
  hsts: true|false
  csp: true|false
  cors-policy: "{description}"

compliance:
  frameworks: ["{GDPR|SOC2|HIPAA|PCI-DSS|none}"]
  audit-logging: true|false
  data-retention: "{policy or unknown}"

known-risks:
  - description: "{risk description}"
    severity: "{high|medium|low}"
    mitigation: "{current mitigation or none}"
```

## Validation

- All auth middleware/decorators discovered must be reflected
- PII fields identified in data model must appear in `data-protection.pii-fields`
- NEVER include actual secrets, keys, or credentials
