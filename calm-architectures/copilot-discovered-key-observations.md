# Key Observations from CALM Architecture Discovery

## Deployment Boundaries

- **calm-hub** is the only containerised backend — Docker images via `Dockerfile.jvm` / `Dockerfile.multistage`, with separate Compose files for `local-dev/`, `deploy/`, and `keycloak-dev/`
- **calm-guard** deploys to Vercel (has `vercel.json`), but requires a Node.js runtime since it shells out to `calm-cli` as a subprocess for validation
- **calm-server** is a standalone npm binary — independent of calm-hub, providing validation-as-a-service over HTTP
- **CalmStudio** (`calm-suite/calm-studio`) is effectively a sub-monorepo (pnpm workspaces) with its own VSCode extension, MCP server, and web app — maintained separately from the main npm workspace

## Authentication / Trust Boundaries

- **calm-hub** has two modes: _unauthenticated_ (default, OIDC disabled) and _secure_ (`-Dquarkus.profile=secure`) where all endpoints require Keycloak JWT bearer tokens with fine-grained scopes (`ARCHITECTURES_ALL`, `ARCHITECTURES_READ`, `SEARCH_READ`, etc.)
- **calm-hub-ui** OIDC is feature-flagged (`AUTH_SERVICE_OIDC_ENABLE=false` by default) and only activates on HTTPS — no auth in plain HTTP deployments
- **calm-guard** has no auth layer of its own — GitHub tokens are handled server-side only and never exposed to the browser client
- **calm-hub-mcp-server** is embedded inside calm-hub and inherits its security boundary, gated by the `calm.mcp.enabled` config property

## External Dependencies

| Dependency | Used By | Notes |
|---|---|---|
| MongoDB | calm-hub | Production storage, `mongodb://localhost:27017`, db `calmSchemas` |
| Keycloak | calm-hub, calm-hub-ui | Optional, secure profile only; `https://calm-hub.finos.org:9443/realms/calm-hub-realm` |
| GitHub API | calm-guard | Requires `GITHUB_TOKEN` env var; used for CALM file fetch and PR creation |
| Google Gemini | calm-guard | Default LLM provider via Vercel AI SDK (`GOOGLE_GENERATIVE_AI_API_KEY`) |
| Anthropic / OpenAI / Grok | calm-guard | Alternative LLM providers, configurable per-agent |

## Sub-Architectures Worth Drilling Into

### calm-hub
14 distinct REST resource endpoints, dual Mongo/Nitrite storage backends selected at runtime via CDI producers, and an embedded MCP server. The store interface layer (`ArchitectureStore`, `PatternStore`, `FlowStore`, `StandardStore`, `SearchStore`, etc.) forms an internal hexagonal architecture. Worthy of a dedicated sub-architecture model.

### calm-guard (4-Agent AI Pipeline)
Agents run in sequence via SSE streaming orchestration:
1. **Scout** — architecture-analyzer: maps components, traces data flows, detects trust/security boundaries
2. **Ranger** — compliance-mapper: maps architecture to regulatory frameworks
3. Risk assessor: evaluates risk exposure
4. **calm-remediator**: generates remediated CALM JSON with added controls and creates a GitHub PR

### CalmStudio MCP Server
7 tool categories (architecture, nodes, relationships, IO, render, guide, view) backed by a shared `calm-core` validation engine. Supports both stdio transport (for Claude Code / Copilot) and HTTP transport. Worthy of its own architecture model.

### calm-cli / calm-shared
The reusable validation and schema-loading engine (`@finos/calm-shared`) is the true core of CALM tooling — consumed by `calm-cli`, `calm-server`, and (via subprocess) `calm-guard`. Any change here affects the entire toolchain.
