# agent-primitives

A collection of reusable VS Code Copilot agent primitives — custom agents, instructions, prompts, and skills — kept in one place.

## Repository Structure

```
agents/          # Custom agent definitions
instructions/    # Reusable instruction files
prompts/         # Prompt files (empty)
skills/          # Skill definitions (empty)
```

## Agents

### `developer.agent.md`

A spec-driven developer agent that implements a feature end-to-end from a specification file. It follows a five-phase workflow: read the spec and all user stories, build a sequenced implementation plan with a dependency graph, implement each story (delegating to sub-agents for complex work), validate changes against acceptance criteria, and update documentation.

### `feature-spec-generator.agent.md`

Generates a complete feature specification and implementation plan. Given a feature name, description, and target personas, it creates a `spec.md` file and individual user story files under a numbered `specs/` directory, covering requirements, design considerations, acceptance criteria, and task breakdowns.

## Instructions

### `mermaid-diagrams.instructions.md`

Conventions for generating Mermaid diagrams in multi-repo system documentation — diagram type selection, node naming, subgraph grouping, edge labeling, color coding, and size guidelines.

### `multi-repo-output.instructions.md`

Output conventions for a multi-repo system documentation framework, defining the Phase 1 (per-repo) and Phase 2 (system-level) folder structure under `architects-metadata/`, file naming rules, and cross-reference linking format.

### `yaml-artifact-format.instructions.md`

Standards for YAML documentation artifacts, including the dual-format rule (YAML + Markdown), key naming (`kebab-case`), required headers, security rules (no secrets), data classification tags, cross-reference format, and validation checklist.
