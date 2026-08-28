---
architecture: calm-architectures/codex-discovered-calm-architecture.json
---

# CALM Architecture Discovery By Codex (GPT 5.4 Medium)

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

        subgraph calm-hub-platform["CALM Hub Platform"]
        direction TB
            calm-hub-api["CALM Hub API"]:::node
            calm-hub-mcp["CALM Hub MCP Server"]:::node
            calm-hub-ui["CALM Hub UI"]:::node
        end
        class calm-hub-platform boundary
        subgraph calm-suite["CALM Suite"]
        direction TB
            calmguard-app["CALMGuard"]:::node
            calmstudio-ui["CalmStudio"]:::node
            calmstudio-mcp["CalmStudio MCP"]:::node
        end
        class calm-suite boundary

    calm-ai-prompts["CALM AI Prompt Pack"]:::node
    calm-cli["CALM CLI"]:::node
    calm-tooling["CALM Tooling"]:::node
    calm-server["CALM Validation Server"]:::node
    calm-vscode["CALM VS Code Extension"]:::node
    llm-providers["External LLM Providers"]:::node
    github-ecosystem["GitHub"]:::node
    keycloak-idp["Keycloak"]:::node
    calm-hub-mongo["MongoDB Store"]:::node
    calm-hub-nitrite["NitriteDB Store"]:::node
    repo-users["Repo Users"]:::node

    repo-users -->|Users explore and manage CALM models through the Hub browser UI.| calm-hub-ui
    calm-hub-ui -->|Hub UI calls the Hub backend /calm endpoints.| calm-hub-api
    repo-users -->|MCP-capable clients can use the Hub MCP endpoint.| calm-hub-mcp
    calm-hub-api -->|Default Hub persistence uses MongoDB.| calm-hub-mongo
    calm-hub-api -->|Standalone Hub mode uses NitriteDB storage.| calm-hub-nitrite
    calm-hub-ui -->|Hub UI can authenticate against Keycloak when OIDC is enabled.| keycloak-idp
    calm-hub-api -->|Secure Hub profile validates authenticated access through Keycloak.| keycloak-idp
    repo-users -->|Users run local CLI commands to generate, validate, and document architectures.| calm-cli
    calm-cli -->|CLI can be pointed at a CalmHub URL for loading documents.| calm-hub-api
    repo-users -->|Users edit CALM files in VS Code with live preview and validation.| calm-vscode
    calm-vscode -->|VS Code workflows consume local CALM prompt assets.| calm-ai-prompts
    repo-users -->|Users invoke the standalone validation API.| calm-server
    repo-users -->|Validation requests are sent to the validation endpoint.| calm-server
    repo-users -->|Users draw architectures visually in CalmStudio.| calmstudio-ui
    repo-users -->|AI clients can drive CalmStudio through its MCP server.| calmstudio-mcp
    repo-users -->|Users upload or fetch CALM models and review compliance dashboards.| calmguard-app
    calmguard-app -->|CALMGuard streams agent progress and findings back to the browser.| repo-users
    calmguard-app -->|CALMGuard calls external LLM providers for analysis and generation.| llm-providers
    calmguard-app -->|CALMGuard fetches CALM from GitHub and produces PR-oriented outputs.| github-ecosystem



```



## Architecture Statistics

- **Total Nodes:** 19
- **Total Relationships:** 25

## Components by Type

### Repo Users

**Type:** `actor`  
**Unique ID:** `repo-users`

#### Description
Developers, architects, and operators using the monorepo tools and user interfaces.





---

### CALM Hub Platform

**Type:** `system`  
**Unique ID:** `calm-hub-platform`

#### Description
Hub-related subsystem combining web UI, backend API, MCP endpoint, storage, and optional authentication.





---

### CALM Hub UI

**Type:** `webclient`  
**Unique ID:** `calm-hub-ui`

#### Description
React and Vite browser UI for browsing and visualizing CALM resources.





---

### CALM Hub API

**Type:** `service`  
**Unique ID:** `calm-hub-api`

#### Description
Quarkus backend serving /calm APIs and Swagger UI.





---

### CALM Hub MCP Server

**Type:** `service`  
**Unique ID:** `calm-hub-mcp`

#### Description
Embedded MCP endpoint exposed by CALM Hub at /mcp.





---

### MongoDB Store

**Type:** `database`  
**Unique ID:** `calm-hub-mongo`

#### Description
Default persistence for CALM Hub in non-standalone mode.





---

### NitriteDB Store

**Type:** `database`  
**Unique ID:** `calm-hub-nitrite`

#### Description
Embedded standalone persistence option for CALM Hub.





---

### Keycloak

**Type:** `ecosystem`  
**Unique ID:** `keycloak-idp`

#### Description
Optional OIDC identity provider for the secure CALM Hub profile.





---

### CALM Tooling

**Type:** `system`  
**Unique ID:** `calm-tooling`

#### Description
Local and IDE-oriented toolchain around CALM authoring and validation.





---

### CALM CLI

**Type:** `service`  
**Unique ID:** `calm-cli`

#### Description
Node-based CLI for generate, validate, template, docify, and AI bootstrap commands.





---

### CALM Validation Server

**Type:** `service`  
**Unique ID:** `calm-server`

#### Description
Standalone HTTP validation service exposing /health and /calm/validate.





---

### CALM VS Code Extension

**Type:** `service`  
**Unique ID:** `calm-vscode`

#### Description
IDE extension for live preview, validation, navigation, and documentation workflows.





---

### CALM AI Prompt Pack

**Type:** `data-asset`  
**Unique ID:** `calm-ai-prompts`

#### Description
Prompt templates and assistant configs consumed by calm init-ai and AI-assisted workflows.





---

### CALM Suite

**Type:** `system`  
**Unique ID:** `calm-suite`

#### Description
Product area containing CalmStudio and CALMGuard.





---

### CalmStudio

**Type:** `webclient`  
**Unique ID:** `calmstudio-ui`

#### Description
Svelte-based visual editor that produces CALM JSON.





---

### CalmStudio MCP

**Type:** `service`  
**Unique ID:** `calmstudio-mcp`

#### Description
MCP server for architecture CRUD, validation, render, and import or export.





---

### CALMGuard

**Type:** `service`  
**Unique ID:** `calmguard-app`

#### Description
Next.js compliance analysis app with SSE streaming and multi-agent orchestration.





---

### External LLM Providers

**Type:** `ecosystem`  
**Unique ID:** `llm-providers`

#### Description
Gemini, Claude, GPT, and Grok providers used by CALMGuard.





---

### GitHub

**Type:** `ecosystem`  
**Unique ID:** `github-ecosystem`

#### Description
Remote repository and pull request platform referenced by CALMGuard and CI workflows.





---



