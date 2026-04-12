---
description: "Generate P1-2 API contracts — api-summary.yaml and api-contracts.md documenting all HTTP/gRPC/GraphQL/message endpoints with request/response schemas, auth requirements, and versioning. Wave 2 artifact. Applies to service repos."
tools: [read, search, execute]
---

Generate **api-summary.yaml** and **api-contracts.md** (P1-2) for the repository at `$input`.

## Applicability

This artifact applies to repos classified as `service`. Skip for `library`, `iac`, `pipeline`, and most `frontend` repos (unless the frontend exposes a BFF/API layer).

## What to Analyze

1. **API definitions**: OpenAPI/Swagger specs, .proto files, GraphQL schemas, route definitions in code
2. **Controller/route files**: Express routes, ASP.NET controllers, Spring @RestController, FastAPI routers
3. **Request/response models**: DTOs, request/response classes, JSON schema definitions
4. **Authentication**: Middleware, decorators, attributes indicating auth requirements (JWT, API key, OAuth)
5. **Versioning**: URL path versioning (`/v1/`, `/v2/`), header-based versioning, content negotiation
6. **Rate limiting and pagination**: Middleware or annotations indicating rate limits, pagination patterns

## Output — api-summary.yaml

Write to `architects-metadata/phase1/{repo-name}/api-summary.yaml`

```yaml
artifact: api-summary
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

api-style: "{REST|gRPC|GraphQL|message-based|mixed}"
base-path: "{base URL path}"
auth-schemes: ["{scheme1}"]
versioning-strategy: "{url-path|header|content-type|none}"
endpoints:
  - path: "{path}"
    method: "{GET|POST|PUT|DELETE|PATCH}"
    summary: "{description}"
    auth-required: true|false
    request-schema: "{reference or inline}"
    response-schema: "{reference or inline}"
    status-codes: [200, 400, 404]
```

## Output — api-contracts.md

Write to `architects-metadata/phase1/{repo-name}/api-contracts.md`

Include:
- Endpoint table (method, path, auth, description)
- Request/response examples for key endpoints
- Sequence diagrams for multi-step flows (using Mermaid `sequenceDiagram`)
- Error response patterns

## Validation

- Every endpoint found in code must appear in the summary
- Auth schemes must match what middleware/decorators enforce
- `api-style` must reflect the actual protocols found
