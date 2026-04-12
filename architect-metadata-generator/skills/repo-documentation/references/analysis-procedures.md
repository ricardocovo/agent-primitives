# Analysis Procedures — Extracting Data from Codebases

Step-by-step procedures for analyzing a repository's source code to populate Phase 1 documentation artifacts.

---

## General Approach

1. **Start with build/package files** — They reveal languages, frameworks, dependencies
2. **Scan configuration** — Env files, config folders, CI/CD pipelines reveal infra and deployment
3. **Analyze source code** — Entry points, controllers, models, event handlers
4. **Check documentation** — Existing READMEs, wikis, ADRs, comments
5. **Review infrastructure** — Dockerfiles, k8s manifests, Terraform, CI/CD

---

## P1-1: Identity Card — Detection Procedures

### Tech Stack Detection

| Signal | Where to Look |
|---|---|
| **Languages** | File extensions, `package.json` (JS/TS), `*.csproj` (.NET), `pom.xml`/`build.gradle` (Java), `requirements.txt`/`pyproject.toml` (Python), `go.mod` (Go), `Cargo.toml` (Rust) |
| **Frameworks** | Package dependencies: Express/Nest/Fastify (Node), ASP.NET/Blazor (.NET), Spring/Micronaut (Java), Django/FastAPI/Flask (Python), Gin/Echo (Go) |
| **Databases** | Migration files, ORM configs, connection strings in config templates, Docker Compose service definitions |
| **Messaging** | Kafka/RabbitMQ/Service Bus client libraries in dependencies, consumer/producer code |
| **Cloud Provider** | IaC files (Terraform provider blocks, Bicep files, AWS CDK), CI/CD deployment targets |

### Owner/Team Detection
- Look for `CODEOWNERS` file
- Check `package.json` `author`/`contributors`
- Check CI/CD pipeline for team/notification channels
- Check README for maintainer info

---

## P1-2: API Contracts — Detection Procedures

### REST APIs
1. Search for OpenAPI/Swagger spec files: `*.openapi.yaml`, `*.swagger.json`, `swagger.yaml`
2. Search for route definitions:
   - **Express**: `app.get(`, `router.post(`, `@Get(`, `@Post(`
   - **ASP.NET**: `[HttpGet]`, `[HttpPost]`, `[Route("")]`, `MapGet(`, `MapPost(`
   - **Spring**: `@GetMapping`, `@PostMapping`, `@RequestMapping`
   - **FastAPI**: `@app.get(`, `@router.post(`
3. For each endpoint: extract path, method, request/response types, auth decorators

### GraphQL
1. Search for `.graphql` schema files
2. Search for resolver definitions
3. Look for schema-first vs code-first patterns

### gRPC
1. Search for `.proto` files
2. Extract service definitions, methods, request/response message types

---

## P1-3: Data Model — Detection Procedures

### Database Schema Discovery
1. **Migration files**: Search for `migrations/`, `db/migrate/`, `src/migrations/`
   - Parse SQL DDL: `CREATE TABLE`, `ALTER TABLE`
   - Parse ORM migrations: EF Core, Alembic, Flyway, Knex
2. **ORM models**: 
   - **EF Core**: Classes with `[Table]` attribute, `DbContext` with `DbSet<>`
   - **Sequelize/TypeORM**: Model definitions with column decorators
   - **SQLAlchemy**: `Base` model classes with `Column()` definitions
   - **Prisma**: `schema.prisma` file
3. **Schema files**: `schema.sql`, `init.sql`, database seed files

### Data Classification
- Fields named `email`, `phone`, `address`, `ssn`, `name` → PII
- Fields named `password`, `token`, `secret`, `key` → Sensitive (should not exist in plain text)
- Audit fields (`created_at`, `updated_at`, `created_by`) → Internal
- IDs, status codes, timestamps → Internal (default)

---

## P1-4: Dependencies — Detection Procedures

### Internal Service Dependencies
1. **HTTP clients**: Search for `HttpClient`, `axios`, `fetch`, `got`, `RestTemplate`, `WebClient` usage
2. **gRPC clients**: Search for generated client stubs
3. **Configuration**: Look for URLs/service names in config files (e.g., `ORDER_SERVICE_URL`, `PAYMENT_API_BASE`)
4. **Service discovery**: Kubernetes service names, Consul, Eureka registrations

### External Dependencies
1. **Third-party API calls**: Search for external URLs in code/config (Stripe, Twilio, SendGrid, etc.)
2. **SaaS integrations**: Auth0, Okta, Datadog, PagerDuty client libraries

### Library Dependencies
1. Parse package manager files: `package.json`, `*.csproj`, `pom.xml`, `requirements.txt`, `go.mod`
2. Focus on key libraries (not devDependencies unless they affect behavior)

### Event Dependencies
1. Search for message producer/consumer patterns (covered in P1-5 procedures)
2. Map topic names to producing/consuming repos

---

## P1-5: Events — Detection Procedures

### Publisher Detection
1. Search for producer/publisher instantiation:
   - **Kafka**: `KafkaProducer`, `producer.send(`, `@KafkaHandler`
   - **RabbitMQ**: `channel.publish(`, `channel.sendToQueue(`
   - **Service Bus**: `ServiceBusSender`, `sender.sendMessages(`
   - **SNS**: `sns.publish(`
   - **EventBridge**: `putEvents(`
2. Extract topic/queue names from the publish calls
3. Find the trigger context (what causes the event to be published)

### Consumer Detection
1. Search for consumer/subscriber patterns:
   - **Kafka**: `@KafkaListener`, `consumer.subscribe(`, consumer group configs
   - **RabbitMQ**: `channel.consume(`, `@RabbitListener`
   - **Service Bus**: `ServiceBusProcessor`, `@ServiceBusQueueTrigger`
   - **SQS**: `sqs.receiveMessage(`
2. Extract handler function names and the topic/queue being consumed
3. Check for idempotency patterns (deduplication, idempotency keys)

---

## P1-6: Configuration — Detection Procedures

### Environment Variables
1. Search for `process.env`, `os.environ`, `Environment.GetEnvironmentVariable`, `System.getenv`
2. Parse `.env.example`, `.env.template`, `.env.sample` files
3. Check Docker Compose `environment:` sections
4. Check Kubernetes `ConfigMap` and `Secret` manifests

### Feature Flags
1. Search for feature flag libraries: LaunchDarkly, Unleash, Azure App Configuration
2. Search for conditional logic patterns: `if (featureEnabled(`, `@Feature(`

### Config Files
1. List all config files: `appsettings*.json`, `config/*.yaml`, `*.toml`, `*.ini`
2. Note their purpose and format

---

## P1-7: Deployment — Detection Procedures

### CI/CD Pipeline
1. Check for pipeline files: `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml`, `.circleci/`
2. Parse stages, triggers, and deployment targets

### Container Configuration
1. Parse `Dockerfile`: base image, exposed ports, entry point
2. Parse `docker-compose.yml`: service definitions, networks, volumes

### Kubernetes
1. Search for k8s manifests: `deployment.yaml`, `service.yaml`, `ingress.yaml`
2. Parse HPA (Horizontal Pod Autoscaler) for scaling rules
3. Parse health check probes (liveness, readiness)

### Cloud Resources
1. Parse IaC files (Terraform, Bicep) for provisioned resources
2. Check CI/CD for cloud deployment commands (az, aws, gcloud)

---

## P1-8: Architecture — Analysis Procedures

1. **Identify entry points**: HTTP routes, message handlers, scheduled tasks, CLI commands
2. **Map internal layers**: Controllers → Services → Repositories → Data Access
3. **Identify design patterns**: Look for pattern names in code/comments, structural indicators (CQRS: separate command/query handlers; Repository: interface + implementation; Saga: orchestration/compensation steps)
4. **Trace error handling**: Try/catch patterns, middleware, circuit breakers, retry policies
5. **Find caching**: Redis/Memcached clients, in-memory cache, cache decorators

---

## P1-9: Security — Detection Procedures

1. **Authentication**: Search for auth middleware, JWT validation, OAuth2 configuration, API key validation
2. **Authorization**: Search for `[Authorize]`, `@PreAuthorize`, role checks, policy definitions
3. **Encryption**: TLS configuration, encryption libraries, key vault references
4. **Compliance**: Search for data handling comments, GDPR annotations, audit logging

---

## P1-10: Domain Context — Analysis Procedures

1. **Read the README** for business context
2. **Analyze domain models** for entity names and business rules
3. **Check folder structure** for bounded context boundaries (e.g., `/modules/orders/`, `/domains/payment/`)
4. **Review validation rules** in models/DTOs for business constraints
5. **Map user journeys** by tracing API endpoints to their business outcomes

---

## P1-11: Operations — Detection Procedures

1. **Metrics/SLIs**: Search for metric definitions, Prometheus exports, Application Insights custom metrics
2. **Alerting**: Check monitoring configs, PagerDuty/OpsGenie integrations, alert definitions
3. **Runbooks**: Check for existing runbooks, playbooks, or operational docs
4. **Health checks**: Already captured in P1-7, cross-reference here
5. **Backup/Recovery**: Check database backup configurations, disaster recovery docs

---

## P1-12: Pipeline Spec — Analysis Procedures

(For data pipeline repos only)

1. **Identify sources**: Input file paths, database connections, API endpoints being read
2. **Map transformations**: ETL steps, data cleaning, aggregation, joins
3. **Identify sinks**: Output destinations (warehouse, lake, API, file)
4. **Parse scheduling**: Cron expressions, Airflow schedules, trigger configurations
5. **Find data quality**: Validation checks, assertion steps, schema enforcement

---

## P1-13: Runtime Behavior — Analysis Procedures

See the `runtime-analysis` skill for detailed procedures on:
- Extracting service call patterns from distributed traces
- Deriving latency percentiles from APM tools
- Identifying hot paths from trace data
- Comparing static dependencies (P1-4) vs. observed dependencies
