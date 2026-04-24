---
description: "Generate P1-12 pipeline.yaml — CI/CD pipeline specification documenting build steps, test stages, deployment gates, artifact publishing, and environment promotion strategy. Wave 3 artifact."
tools: [read, search, execute]
---

Generate **pipeline.yaml** (P1-12) for the repository at `$input`.

## What to Analyze

1. **CI/CD definitions**: `.github/workflows/*.yml`, `.azure-pipelines.yml`, `Jenkinsfile`, `.gitlab-ci.yml`, `bitbucket-pipelines.yml`
2. **Build steps**: Compile, test, lint, security scan, Docker build, artifact publish
3. **Test stages**: Unit tests, integration tests, E2E tests, contract tests — which run and where
4. **Deployment stages**: Dev, staging, production — triggers, gates, approvals
5. **Artifact publishing**: Container registry push, package feed publish (NuGet, npm, Maven)
6. **Quality gates**: Code coverage thresholds, static analysis, dependency scanning, SAST/DAST
7. **Environment promotion**: How code moves from dev → staging → production

## Output

Write to `architects-metadata/phase1/{repo-name}/pipeline.yaml`

```yaml
artifact: pipeline
version: "1.0"
generated: "{ISO-8601 date}"
repo-name: "{name}"

ci-platform: "{GitHub-Actions|Azure-DevOps|Jenkins|GitLab-CI|CircleCI}"
trigger:
  branches: ["{branch patterns}"]
  paths: ["{path filters or all}"]
  type: "{push|PR|schedule|manual}"

stages:
  - name: "{stage name}"
    steps:
      - name: "{step name}"
        tool: "{tool or action}"
        purpose: "{what it does}"
    environment: "{env if deployment stage}"
    gate: "{approval requirement or auto}"

test-strategy:
  unit-tests:
    framework: "{test framework}"
    coverage-threshold: "{percentage or none}"
  integration-tests:
    present: true|false
    approach: "{description}"
  e2e-tests:
    present: true|false
    approach: "{description}"

artifacts:
  - type: "{container|package|binary|static-site}"
    registry: "{where published}"
    naming: "{naming convention}"

quality-gates:
  - name: "{gate name}"
    tool: "{tool}"
    threshold: "{pass criteria}"

environment-promotion:
  strategy: "{sequential|parallel|canary|blue-green}"
  environments: ["{dev}", "{staging}", "{production}"]
  rollback: "{rollback approach}"
```

## Validation

- Every CI/CD workflow file must be reflected in the output
- All stages/steps in the pipeline definition must be documented
- Quality gate thresholds must match actual configurations
