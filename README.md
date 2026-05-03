# CALM Practitioner Skills — Discovery Skill Proof of Concept

This repository is focused on demonstrating a proof-of-concept for the [`calm-architecture-discovery`](./.github/skills/calm-architecture-discovery/SKILL.md) skill and capturing example CALM outputs from different AI assistants.

The `calm-architecture-discovery` skill generates a CALM architecture by scanning application source code. This provides a means to quickly produce a CALM architecture model without manual effort — though the result may contain inaccuracies or omissions. The intent is to give an architect a well-structured starting point that they can review and refine to make it accurate.

Although the discovered architectures from the different AI Assistants contain meaningful semantic differences, each is still useful as a starting point for an architect refining the model. Each captures the same core architecture landscape around CALM Hub, the Hub UI, CLI-based authoring and validation, persistence, authentication, and AI-assisted workflows.

**Important note**:

When the `calm-architecture-discovery` skill is used, the CALM AI Assistant displays the following warning before showing discovered architecture results:

> ⚠️ This is an initial discovery of potential CALM nodes and relationships based on static analysis of the codebase by an LLM. It may contain inaccuracies or omissions. Please review and validate each item before using it in your CALM architecture model.

## Purpose

- Provide sample inputs and recorded outputs from multiple CALM-capable assistants (Claude, Codex variants, Copilot).
- Give a reproducible workflow for exercising the `calm-architecture-discovery` skill and comparing results.

## Repository structure

- calm-architectures/
  | File Name | Description |
  | --- | --- |
  | [claude-discovered-calm-architecture.json](calm-architectures/claude-discovered-calm-architecture.json) | CALM model produced by Claude |
  | [claude-discovered-calm-architecture.md](calm-architectures/claude-discovered-calm-architecture.md) | CALM docify architecture document for the architecture discovered by Claude |
  | [claude-discovered-key-observations.md](calm-architectures/claude-discovered-key-observations.md) | Human-readable observations from Claude |
  | [codex-5.5-high-discovered-calm-architecture.json](calm-architectures/codex-5.5-high-discovered-calm-architecture.json) | CALM model from Codex (5.5 high) |
  | [codex-5.5-high-discovered-calm-architecture.md](calm-architectures/codex-5.5-high-discovered-calm-architecture.md) | CALM docify architecture document for the architecture discovered by Codex (5.5 high) |
  | [codex-5.5-high-discovered-key-observations.md](calm-architectures/codex-5.5-high-discovered-key-observations.md) | Observations from Codex (5.5 high) |
  | [codex-discovered-calm-architecture.json](calm-architectures/codex-discovered-calm-architecture.json) | CALM model from Codex |
  | [codex-discovered-calm-architecture.md](calm-architectures/codex-discovered-calm-architecture.md) | CALM docify architecture document for the architecture discovered by Codex |
  | [codex-discovered-key-observations.md](calm-architectures/codex-discovered-key-observations.md) | Observations from Codex |
  | [copilot-discovered-calm-architecture.json](calm-architectures/copilot-discovered-calm-architecture.json) | CALM model from Copilot |
  | [copilot-discovered-calm-architecture.md](calm-architectures/copilot-discovered-calm-architecture.md) | CALM docify architecture document for the architecture discovered by Copilot |
  | [copilot-discovered-key-observations.md](calm-architectures/copilot-discovered-key-observations.md) | Observations from Copilot |

- templates/
  - solution-architecture-document.md — Template for turning discovered CALM models into a solution document

## Getting started — proof of concept workflow

1. Enable CALM AI Assistant `calm init-ai` and manually added the `calm-architecture-discovery` skill to the correct location for the particular AI Assistant.
2. Enable the `calm` skill to prepare the agent for architecture discovery.
3. Run the `calm-architecture-discovery` skill to discover the architecture implied by the code.
4. Save the resulting CALM JSON and key-observations files into `calm-architectures/` for each assistant variant you evaluate.

Here is an example [CALM AI Assistant Chat log](copilot-architecture-discovery-chat-log.md) using `/calm-architecture-discovery`.  

For the discovered architecture this is the CALM [generated documentation](./calm-architectures/copilot-discovered-calm-architecture.md) of the architecture.


## Architecture comparison

The four CALM architectures in `calm-architectures/` are not semantically identical, although they do describe the same broad problem space.

### Shared semantic core

- All four models identify the CALM toolchain or monorepo as being centered on the same primary capabilities: a developer-facing experience, CALM Hub or its API, a browser UI, CLI-based authoring and validation, a standalone validation server, persistent storage, and optional Keycloak-backed authentication.
- All four models also recognize AI-assisted interaction as part of the ecosystem, either through a dedicated AI assistant actor, MCP endpoints, CalmStudio MCP, CALMGuard agents, or AI prompt assets.

### Validation results

- `claude-discovered-calm-architecture.json` passes `calm validate -a` with no errors and no warnings.
- `codex-5.5-high-discovered-calm-architecture.json` passes `calm validate -a` with no errors and no warnings.
- `codex-discovered-calm-architecture.json` passes `calm validate -a` with no errors and one warning: node `calm-tooling` is not referenced by any relationship.
- `copilot-discovered-calm-architecture.json` passes `calm validate -a` with no errors and two warnings: nodes `docs-site` and `advent-of-calm-site` are not referenced by any relationship.

### Architect observations

- The models use different system boundaries. Claude and Copilot mostly model runtime products and external services, while Codex 5.5 high also models the repository itself plus internal packages such as `calm-shared`, `calm-models`, and `calm-widgets`.
- The models disagree on decomposition of CALM Hub. Some treat Hub and MCP as separate services, while Claude models MCP as an interface exposed by the Hub service. That is an important architectural choice, not just naming drift.
- CALMGuard and CalmStudio are inconsistently represented. Some models include them as first-class products with their own MCP or agent subcomponents, while Claude omits CalmStudio MCP and Copilot models CALMGuard as a webclient rather than splitting UI and service concerns.
- Deployment and publishing concerns are unevenly captured. Claude includes Kubernetes, Docker Hub, AWS S3, and GitHub Actions explicitly; Codex 5.5 high captures similar concerns with slightly different abstraction; the other two are lighter and therefore less useful for deployment or release architecture review.
- The baseline Codex model introduces higher-level container nodes such as `calm-hub-platform`, `calm-tooling`, and `calm-suite`. That makes the model easier to scan, but it changes the semantics because it mixes conceptual grouping with deployable or executable elements.
- The warning-producing models are still valid, but the unreferenced nodes are a sign of incomplete story-telling. An architect reviewing those files would likely ask whether those nodes belong in scope or whether relationships are missing.

## Conclusion

These architectures are best understood as different abstractions of the same landscape rather than semantically equivalent models.

Although the discovered CALM architectures are not semantically identical, they are broadly comparable as proof-of-concept discovery outputs. Each captures the same core architecture landscape around CALM Hub, the Hub UI, CLI-based authoring and validation, persistence, authentication, and AI-assisted workflows. That makes all of them useful as starting points for an architect who wants to refine the model further. The main differences are in abstraction level, scope, and completeness rather than in the fundamental understanding of the system. In practice, the strongest starting points are the models that validate cleanly and include more operational context, because they require less refinement before deeper architecture review.
