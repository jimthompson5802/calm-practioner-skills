# CALM Architecture Discovery — Key Observations

> Discovered: 2026-05-02  
> Source: Static analysis of `https://github.com/finos/architecture-as-code`  
> Analyst: Claude (claude-sonnet-4-6)

---

## Deployment Boundaries

- **Docker Compose** (`calm-hub/deploy/docker-compose.yml`): `calm-hub` + `mongodb` share a `calm-net` bridge network. This is the primary local/self-hosted deployment unit.
- **Kubernetes** (`calm-hub/k8s/`): `calmhub` Deployment (LoadBalancer, 80→8080) + `mongodb` Deployment (ClusterIP, 27017). No Helm charts found — raw manifests only.
- **Standalone mode**: `calm-hub` can run without any external database by switching to the embedded NitriteDB, making it a single-binary deployment.
- **CI/CD boundary**: GitHub Actions is the external orchestration layer; it is the only actor that pushes artefacts to Docker Hub and AWS S3.

---

## Authentication / Trust Boundaries

- **Default profile**: No authentication enforced — CALM Hub accepts all requests. Intended for localhost/trusted-network use only.
- **Secure profile** (`application-secure.properties`): Keycloak OIDC enforced on all Hub endpoints. Scope-based authorisation (`ARCHITECTURES_ALL`, `CONTROLS_READ`, etc.) gates every resource type independently.
- **MCP endpoint** (`/mcp`): Served on the same port as the REST API (8080). Its authentication posture should be confirmed — if the default profile is active, this endpoint is unauthenticated.
- **UI ↔ Keycloak**: The browser-side OIDC flow is handled by `oidc-client-ts`; access tokens are forwarded to the Hub as Bearer headers via Axios.
- **calm-server**: No authentication — pure validation utility, only exposed on `127.0.0.1` by default.

---

## External Dependencies

| Dependency | Role | Optionality |
|---|---|---|
| MongoDB | Persistent store | Optional — replaced by NitriteDB in standalone mode |
| Keycloak | Identity / OIDC | Optional — only required in `secure` profile |
| Docker Hub | Image distribution | Required for containerised deployments |
| AWS S3 | Documentation hosting | CI/CD only; not required by runtime |
| GitHub Actions | Build & release | CI/CD only; not required by runtime |

---

## Nodes That Likely Warrant Sub-Architectures

- **`calm-hub`** — The most complex node. Internally contains at minimum: REST API layer, MCP layer, pluggable store abstraction (MongoDB / Nitrite), CDI producer-based runtime store selection, security filter chain, rate limiter, and HTML sanitiser. A `detailed-architecture` drilling into these internal components would be valuable.
- **`calm-cli`** — Contains distinct sub-commands (`generate`, `validate`, `docify`, `template`, `init-ai`) each with their own dependency graph (schema resolver, Spectral engine, Handlebars renderer). Worth a sub-architecture.
- **`calm-hub-ui`** — Composed of several independent service clients (`calm-service`, `control-service`, `adr-service`, `search-service`, `interface-service`) and a graph visualisation engine. A sub-architecture would clarify the frontend component boundaries.
- **`calm-guard`** — Role is unclear from static analysis alone. The existence of a dedicated CI workflow (`build-calm-guard.yml`) and a separate build suggests it is a non-trivial independent service that deserves its own architecture document once its responsibilities are confirmed.

---

## Additional Notes

- The monorepo is polyglot: Java/Quarkus (calm-hub), TypeScript/Node.js (CLI, shared, models, widgets, server, hub-ui), and Electron (calm-studio).
- Node version is pinned to 22.14.0 via `.nvmrc` with engine-strict enforcement in `.npmrc`.
- Security scanning (CVE, licence, Semgrep) runs as separate CI workflows for both Maven and Node.js dependency trees.
- The `calm-schemas` data-asset is embedded/bundled into both `calm-server` and `calm-cli` at build time, enabling fully offline validation without a running Hub.
- `calm-hub` serves the built `calm-hub-ui` static assets directly from `META-INF/resources`, meaning a single container can serve both API and UI in production.
