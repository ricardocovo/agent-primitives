---
description: "Generate P1-7 deployment.yaml — deployment specification documenting hosting platform, container config, scaling rules, resource allocations, networking, and health probes. Wave 4 artifact. Applies to service, frontend, and pipeline repos."
tools: [read, search, execute]
---

Generate **deployment.yaml** (P1-7) for the repository at `$input`.

## Applicability

This artifact applies to repos that have deployment configurations (Dockerfiles, Kubernetes manifests, serverless configs, static hosting configs). Skip for `library` repos and repos with no deployment artifacts.

## What to Analyze

1. **Container config**: Dockerfile, docker-compose.yml, container registry references
2. **Orchestration**: Kubernetes manifests (Deployment, Service, Ingress, HPA), Helm charts, Kustomize overlays
3. **Serverless**: Azure Functions host.json, AWS Lambda SAM templates, serverless.yml
4. **Static hosting**: Azure Static Web Apps, S3 + CloudFront, Netlify/Vercel configs
5. **Scaling**: HPA, KEDA, auto-scaling rules, min/max replicas
6. **Health probes**: Liveness, readiness, startup probes
7. **Networking**: Ingress rules, load balancer configs, service mesh (Istio, Linkerd), DNS

## Output

Write to `architects-metadata/phase1/{repo-name}/deployment.yaml`

```yaml
artifact: deployment
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

platform: "{Kubernetes|Azure-App-Service|Azure-Functions|AWS-Lambda|Static-Web-App|VM|mixed}"
container:
  dockerfile: "{path}"
  base-image: "{image:tag}"
  exposed-ports: [8080]
  registry: "{registry URL}"

orchestration:
  type: "{Kubernetes|Helm|Kustomize|none}"
  namespace: "{namespace}"
  replicas:
    min: 1
    max: 5
  scaling:
    type: "{HPA|KEDA|manual}"
    metrics: ["{cpu|memory|custom}"]
    target: "{threshold}"

resources:
  requests:
    cpu: "{value}"
    memory: "{value}"
  limits:
    cpu: "{value}"
    memory: "{value}"

health-probes:
  liveness:
    path: "{path}"
    port: 8080
    period-seconds: 30
  readiness:
    path: "{path}"
    port: 8080

networking:
  ingress: "{path or null}"
  service-type: "{ClusterIP|LoadBalancer|NodePort}"
  tls: true|false

environments:
  - name: "{dev|staging|production}"
    differences: "{key differences from base}"
```

## Validation

- Container config must match Dockerfile if present
- All Kubernetes manifests must be reflected in the output
- Health probe paths must match actual endpoints in the service
