# Key Observations from CALM Architecture Discovery

## Deployment Boundaries

- **`calm-hub`** is the only containerised backend service — Dockerfiles (`Dockerfile.jvm`, `Dockerfile.multistage`) exist under `calm-hub/`; three separate Docker Compose files cover `local-dev/`, `deploy/`, and `keycloak-dev/` topologies.
- **`calm-hub-ui`** has no dedicated container; in production its Vite build output is served as static assets by the Quarkus application inside the calm-hub container — it is not a separate deployable unit.
- **`calm-server`** is a standalone npm binary (`calm-server` CLI), deployable independently of calm-hub. It provides validation-as-a-service with no authentication, binding to `127.0.0.1:3000` by default.
- **`calm-guard`** targets Vercel deployment (`vercel.json` present); it must run on the Node.js runtime because it shells out to `calm-cli` as a subprocess for validation.
- **`calm-studio`** is effectively a sub-monorepo (`pnpm` workspaces under `calm-suite/calm-studio`) with its own package manager and release pipeline, maintained separately from the root npm workspace.
- **`calm-studio-mcp-server`** supports two transport modes: stdio (default, for Claude Code / MCP clients) and HTTP on port 3100 (`--http --port` flags).

## Authentication / Trust Boundaries

- **Keycloak** is an optional security layer: CALM Hub runs with OIDC disabled by default (`quarkus.oidc.tenant-enabled=false`); the `secure` Spring profile activates TLS (port 8443) and enforces JWT authentication on all `/calm/*` and `/mcp/*` paths.
- **CALM Hub UI** OIDC is also opt-in (`AUTH_SERVICE_OIDC_ENABLE = false`) and only activates when the page is served over HTTPS, aligning with the backend secure profile.
- **`calm-server`** has **no authentication or authorisation controls** — the startup code actively warns when the server is bound to non-localhost addresses.
- **`calm-guard`** calls `github-api` with an optional `GITHUB_TOKEN` — public repositories work without a token (reduced rate limits only).

## External Dependencies

- **LLM Providers**: CALMGuard supports four competing LLM providers (OpenAI, Anthropic, Google Gemini, xAI), selected at runtime based on environment-variable API keys (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `GOOGLE_GENERATIVE_AI_API_KEY`, `XAI_API_KEY`).
- **GitHub API**: CALMGuard has a direct integration to `api.github.com` for both fetching source CALM files and writing back generated artefacts via pull requests.
- **Docker Hub**: The `finos/calm-hub` multi-arch image is published to Docker Hub via GitHub Actions; there is no self-hosted container registry in evidence.
- **AWS S3**: Two separate sync workflows exist — `s3-docs-sync.yml` for the Docusaurus documentation site and `s3-video-sync.yml` for video assets.

## Nodes with Sub-Architectures Worth Drilling Into

- **`calm-hub`** — exposes 15+ REST resource endpoints (architectures, patterns, flows, standards, interfaces, controls, ADRs, decorators, domains, namespaces, search, front-controller) plus an embedded MCP server at `/mcp`. Two pluggable persistence backends — MongoDB (default) and embedded NitriteDB (standalone mode) — are selected via CDI producers at startup. Warrants its own detailed architecture.
- **`calm-guard`** — contains a multi-agent AI orchestrator with four specialist agents (architecture analyser, compliance mapper, risk scorer, calm remediator) and a CI/CD pipeline generator. Its Next.js App Router structure with SSE streaming, subprocess-based validation, and multi-provider LLM integration merits a dedicated sub-architecture and flow model.
- **`calm-studio`** — the sub-monorepo contains six distinct packages (`calm-core`, `calmscript`, `extensions`, `mcp-server`, `vscode-extension`, `web-component`, `github-action`) each with independent release cycles. The desktop app (`apps/studio`) wraps a SvelteKit UI in a Tauri native container. Each package is a candidate for its own node in a more detailed architecture.
