---
architecture: calm-architectures/copilot-discovered-calm-architecture.json
---

# CALM Architecture Discovery By Github Copilot (Claude Sonnet 4.6)

## System Architecture
```mermaid
---
config:
  theme: base
  themeVariables:
    fontFamily: -apple-system, BlinkMacSystemFont, 'Segoe WPC', 'Segoe UI', system-ui, 'Ubuntu', sans-serif
    darkMode: false
    fontSize: 14px
    edgeLabelBackground: '#d5d7e1'
    lineColor: '#000000'
---
%%{init: {"layout": "elk", "flowchart": {"htmlLabels": false}}}%%
flowchart TB
classDef boundary fill:#e1e4f0,stroke:#204485,stroke-dasharray: 5 4,stroke-width:1px,color:#000000;
classDef node fill:#eef1ff,stroke:#007dff,stroke-width:1px,color:#000000;
classDef iface fill:#f0f0f0,stroke:#b6b6b6,stroke-width:1px,font-size:10px,color:#000000;
classDef highlight fill:#fdf7ec,stroke:#f0c060,stroke-width:1px,color:#000000;

        subgraph calm-hub["CALM Hub"]
        direction TB
            calm-hub-mcp-server["CALM Hub MCP Server"]:::node
        end
        class calm-hub boundary

    advent-of-calm-site["Advent of CALM Site"]:::node
    ai-assistant["AI Assistant"]:::node
    calm-cli["CALM CLI"]:::node
    docs-site["CALM Documentation Site"]:::node
    calm-hub-ui["CALM Hub UI"]:::node
    calm-server["CALM Server"]:::node
    calm-specification["CALM Specification"]:::node
    calm-guard["CALMGuard"]:::node
    calmstudio-mcp-server["CalmStudio MCP Server"]:::node
    calmstudio-vscode-extension["CalmStudio VSCode Extension"]:::node
    developer["Developer / Architect"]:::node
    vscode-extension-finos["FINOS CALM VSCode Extension"]:::node
    github-api["GitHub API"]:::node
    keycloak["Keycloak"]:::node
    llm-providers["LLM Providers"]:::node
    mongodb["MongoDB"]:::node
    nitritedb["NitriteDB"]:::node

    developer -->|Browses, searches, and views CALM artifacts using| calm-hub-ui
    developer -->|Submits CALM architectures for AI-driven compliance analysis using| calm-guard
    developer -->|Generates, validates, templates, and documents CALM architectures using| calm-cli
    developer -->|Edits and validates CALM models inside VS Code using| vscode-extension-finos
    developer -->|Authors CALM architectures with AI-assisted tooling via| calmstudio-vscode-extension
    ai-assistant -->|Queries patterns, architectures, and standards; executes CALM operations via MCP tools exposed by| calm-hub-mcp-server
    ai-assistant -->|Creates, updates, validates, and renders CALM architecture files via MCP tools provided by| calmstudio-mcp-server
    calm-hub-ui -->|Fetches namespaces, patterns, architectures, flows, standards, ADRs, controls, decorators, and search results from| calm-hub
    calm-hub-ui -->|Authenticates users via OIDC authorization code flow with; obtains and refreshes JWT bearer tokens from| keycloak
    calm-hub -->|Persists and retrieves all CALM artifacts #40;architectures, patterns, flows, standards, etc.#41; in production mode from| mongodb
    calm-hub -->|Persists and retrieves CALM artifacts in standalone/embedded mode from #40;no network; in-process#41;| nitritedb
    calm-hub -->|Validates incoming OIDC JWT bearer tokens and resolves user scopes/roles against| keycloak
    calm-guard -->|Fetches CALM JSON files from user-specified repositories and creates remediation pull requests via| github-api
    calm-guard -->|Sends architecture analysis prompts to and receives structured compliance/risk results from| llm-providers
    calm-guard -->|Invokes as a Node.js subprocess to validate CALM document JSON at the /api/calm/validate route| calm-cli
    calmstudio-vscode-extension -->|Launches and connects to via stdio transport, registering it as an MCP provider for VS Code Copilot| calmstudio-mcp-server
    calm-cli -->|Reads and bundles CALM meta schemas to validate architectures against patterns and generate pattern scaffolds from| calm-specification
    calm-server -->|Loads bundled CALM meta schemas at startup to validate submitted architecture JSON payloads against| calm-specification



```



## Architecture Statistics

- **Total Nodes:** 19
- **Total Relationships:** 19

## Components by Type

### Developer / Architect

**Type:** `actor`  
**Unique ID:** `developer`

#### Description
A software developer or architect who authors, validates, and manages CALM architecture models using the CLI, IDE extensions, or web UIs





---

### AI Assistant

**Type:** `actor`  
**Unique ID:** `ai-assistant`

#### Description
An AI coding assistant (GitHub Copilot, Claude Code, etc.) that interacts with CALM via MCP tool servers to author and query architectures





---

### CALM Hub UI

**Type:** `webclient`  
**Unique ID:** `calm-hub-ui`

#### Description
React 19 SPA (Vite build) for browsing, searching, and visualizing CALM artifacts stored in CALM Hub; uses OIDC for optional authentication





---

### CALMGuard

**Type:** `webclient`  
**Unique ID:** `calm-guard`

#### Description
Next.js 15 full-stack web application that runs a 4-agent AI pipeline to analyze CALM architectures for compliance and risk





---

### CALM Documentation Site

**Type:** `webclient`  
**Unique ID:** `docs-site`

#### Description
Docusaurus-based public documentation website served at calm.finos.org





---

### Advent of CALM Site

**Type:** `webclient`  
**Unique ID:** `advent-of-calm-site`

#### Description
Astro-based educational website hosting the 24-day CALM architecture challenge





---

### CALM Hub

**Type:** `service`  
**Unique ID:** `calm-hub`

#### Description
Java/Quarkus REST API backend — central registry for CALM architectures, patterns, flows, standards, interfaces, controls, ADRs, and decorators, namespaced across domains





---

### CALM Hub MCP Server

**Type:** `service`  
**Unique ID:** `calm-hub-mcp-server`

#### Description
Model Context Protocol server embedded inside CALM Hub, exposing CALM operations as JSON-RPC tools to AI assistants via the /mcp HTTP endpoint





---

### CALM Server

**Type:** `service`  
**Unique ID:** `calm-server`

#### Description
TypeScript/Express HTTP server exposing a /calm/validate REST endpoint and /health check; bundles CALM meta schemas for offline validation





---

### CALM CLI

**Type:** `service`  
**Unique ID:** `calm-cli`

#### Description
TypeScript CLI (@finos/calm-cli) providing generate, validate, template, docify, and init-ai commands for CALM model lifecycle management





---

### FINOS CALM VSCode Extension

**Type:** `service`  
**Unique ID:** `vscode-extension-finos`

#### Description
VSCode extension (calm-plugins/vscode) for editing, validating, and visualizing CALM architecture models inside the IDE





---

### CalmStudio MCP Server

**Type:** `service`  
**Unique ID:** `calmstudio-mcp-server`

#### Description
Model Context Protocol server (calm-suite/calm-studio) providing architecture authoring tools to AI assistants via stdio or HTTP transport





---

### CalmStudio VSCode Extension

**Type:** `service`  
**Unique ID:** `calmstudio-vscode-extension`

#### Description
VSCode extension (calm-suite/calm-studio) that registers the CalmStudio MCP server as a VS Code Copilot tool provider





---

### MongoDB

**Type:** `database`  
**Unique ID:** `mongodb`

#### Description
MongoDB document database — production storage backend for CALM Hub; stores all CALM artifacts in namespace-scoped collections





---

### NitriteDB

**Type:** `database`  
**Unique ID:** `nitritedb`

#### Description
Embedded NitriteDB document store — standalone/local-dev storage backend for CALM Hub, requiring no external database process





---

### Keycloak

**Type:** `ecosystem`  
**Unique ID:** `keycloak`

#### Description
OpenID Connect identity provider for CALM Hub authentication; issues JWT bearer tokens and manages the calm-hub-realm with role-based scopes





---

### GitHub API

**Type:** `ecosystem`  
**Unique ID:** `github-api`

#### Description
GitHub REST API used by CALMGuard to fetch CALM architecture files from public/private repositories and to create remediation pull requests





---

### LLM Providers

**Type:** `ecosystem`  
**Unique ID:** `llm-providers`

#### Description
External AI language model APIs (Google Gemini, Anthropic Claude, OpenAI GPT, Grok) consumed by CALMGuard&#x27;s agent pipeline via the Vercel AI SDK





---

### CALM Specification

**Type:** `data-asset`  
**Unique ID:** `calm-specification`

#### Description
Versioned CALM JSON meta schemas (calm/ directory, release/1.2 and earlier) defining the canonical node, relationship, interface, flow, and control schemas





---



