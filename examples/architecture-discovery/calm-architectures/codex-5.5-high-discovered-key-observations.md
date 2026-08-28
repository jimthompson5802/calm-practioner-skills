# Key Observations

## Deployment Boundaries

- CALM Hub is the clearest deployable backend. It has Dockerfiles, Docker Compose files, Kubernetes manifests, and a Quarkus runtime.
- CALM Hub UI is a separate React/Vite workspace during development, but its production build is copied into the CALM Hub backend resources.
- MongoDB is the default CALM Hub persistence boundary. NitriteDB is available as an embedded standalone storage mode.
- CALM Server is a separate Express-based validation service exposing `/health` and `/calm/validate`.
- CALMGuard and CalmStudio are separate application suites under `calm-suite/`, with their own package managers and runtime boundaries.

## Authentication And Trust Boundaries

- CALM Hub's default profile leaves the OIDC tenant disabled, while the secure profile protects `/calm/*` and `/mcp/*` with authenticated access.
- Keycloak is used for local secure-profile OIDC development, with Hub UI using authorization code flow and Hub validating JWTs.
- CALM Server has no built-in authentication and is intended to bind to localhost by default unless explicitly exposed.
- CALMGuard crosses external trust boundaries to GitHub and LLM providers, using API tokens and provider keys.
- MCP surfaces exist in both CALM Hub and CalmStudio, so authentication and exposure settings matter when those endpoints are reachable beyond localhost.

## External Dependencies

- GitHub is used for source hosting, CI workflows, release automation, repository API access, and remediation pull requests.
- Docker Hub is used for publishing CALM Hub container images.
- AWS S3 and CloudFront are used by documentation publishing workflows.
- Keycloak provides local OIDC identity services for secure Hub development.
- MongoDB provides Hub persistence in the default storage mode.
- LLM provider APIs include Google Gemini, Anthropic Claude, OpenAI, and xAI for CALMGuard agent workflows.
- `calm.finos.org` is both the public documentation site and a canonical source for CALM schema URLs.

## Sub-Architectures Worth Drilling Into

- CALM Hub: REST resources, MCP tools, store interfaces, Mongo and Nitrite implementations, producer selection, OIDC security, and deployment manifests.
- CALM Hub UI: service layer, auth service, visualization workspace, Hub navigation, ADR rendering, and architecture graph rendering.
- CALM CLI and shared library: document loaders, schema validation, Spectral rules, generation, templating, docify, and AI assistant setup.
- CALMGuard: Next.js API routes, SSE event streaming, GitHub integration, deterministic learning engine, LLM agent orchestration, and compliance skills.
- CalmStudio: SvelteKit editor, CALM canvas model, bidirectional JSON sync, import/export, Tauri desktop boundary, and MCP server tools.
- VS Code extension: file watching, model indexing, preview webview, tree navigation, validation, and docify integration.

