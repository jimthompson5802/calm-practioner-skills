⚠️ This is an initial discovery of potential CALM nodes and relationships based on static analysis of the codebase by an LLM. It may contain inaccuracies or omissions. Please review and validate each item before using it in your CALM architecture model.

# Codex Discovered Key Observations

- Deployment boundaries: `calm-hub` is the clearest deployable runtime boundary. It includes a browser UI, Quarkus API, embedded MCP endpoint, MongoDB default storage, and optional Keycloak-backed secure mode.
- Deployment boundaries: `calm-server` is a separate lightweight validation service and appears independently deployable from the rest of the stack.
- Deployment boundaries: `calm-suite` introduces another independently deployable area with `CalmStudio` and `CALMGuard`, separate from the older npm workspace and Maven reactor structure.
- Authentication and trust boundaries: OIDC is disabled by default in Hub, but the secure profile protects both `/calm/*` and `/mcp/*`; this creates an important boundary between anonymous local or dev use and authenticated production use.
- Authentication and trust boundaries: `CALMGuard` crosses a strong trust boundary because it reaches external LLM providers and GitHub, and streams AI-generated results back to users.
- External dependencies: MongoDB, Keycloak, GitHub, and external LLM providers are the main third-party or platform dependencies surfaced directly in code and docs.
- External dependencies: MCP is a recurring integration surface in both `calm-hub` and `CalmStudio`, which suggests AI-client interoperability is a first-class architectural concern.
- Drill-down candidates: `calm-hub-api` likely deserves its own sub-architecture for resource stores, REST resources, and MCP tools.
- Drill-down candidates: `calmguard-app` likely deserves its own sub-architecture for agent orchestration, SSE streaming, provider adapters, and GitHub integration.
- Drill-down candidates: `calmstudio-ui` plus `calmstudio-mcp` also look rich enough for a separate model because they combine visual editing, code sync, import and export, and AI control surfaces.
