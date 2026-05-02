---
name: calm-architecture-discovery
description: Use when asked to scan a codebase to identify CALM nodes and relationships for a FINOS CALM architecture model.
user-invocable: true
---

# CALM Node & Relationship Discovery Skill

Scan the codebase to identify potential CALM nodes and relationships for a FINOS CALM architecture model (schema version 1.2).

## What to look for

### Nodes — identify each distinct architectural component:
- **actor** — humans, external systems, or clients that initiate interactions
- **webclient** — browser-based UIs, SPAs, dashboards
- **service** — APIs, microservices, background workers, CLIs, servers
- **database** — any data store (SQL, NoSQL, embedded, in-memory)
- **network** — load balancers, API gateways, proxies, ingress controllers
- **system** — logical groupings of services (bounded contexts, subsystems)
- **ecosystem** — external third-party platforms (cloud providers, SaaS, identity providers)
- **data-asset** — files, datasets, message queues, event streams

### Evidence to scan

#### Package manifests & build files (pick what applies)
| Ecosystem | Files |
|-----------|-------|
| JavaScript / TypeScript | `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml` |
| Java / Kotlin / Scala | `pom.xml`, `build.gradle`, `build.gradle.kts`, `settings.gradle` |
| Python | `pyproject.toml`, `setup.py`, `setup.cfg`, `requirements*.txt`, `Pipfile`, `poetry.lock` |
| C / C++ | `CMakeLists.txt`, `Makefile`, `conanfile.txt`, `vcpkg.json` |
| C# / .NET | `*.csproj`, `*.sln`, `nuget.config`, `packages.config` |
| Go | `go.mod`, `go.sum` |
| Rust | `Cargo.toml`, `Cargo.lock` |
| Ruby | `Gemfile`, `Gemfile.lock`, `*.gemspec` |
| PHP | `composer.json`, `composer.lock` |
| Swift / Objective-C | `Package.swift`, `Podfile`, `*.xcodeproj` |
| Elixir / Erlang | `mix.exs`, `rebar.config` |

#### Deployment & infrastructure
- **Containers**: `Dockerfile*`, `docker-compose*.yml`, `.dockerignore`
- **Kubernetes**: `*.yaml`/`*.yml` in `k8s/`, `deploy/`, `helm/`, `charts/` — look for `kind: Deployment`, `Service`, `Ingress`, `ConfigMap`
- **Serverless**: `serverless.yml`, `template.yaml` (SAM), `terraform/` (`*.tf`), `pulumi/`, `cdk/`, `bicep/`
- **CI/CD**: `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/`, `azure-pipelines.yml`

#### Configuration & environment
- `application.properties`, `application.yml`, `application*.yaml` (Spring Boot)
- `appsettings.json`, `appsettings.*.json` (.NET)
- `config.py`, `settings.py`, `config.yaml`, `config.toml`
- `.env`, `.env.*`, `*.env.example` — look for URLs, hostnames, port numbers, database connection strings
- `config/`, `conf/`, `settings/` directories

#### Source code signals
- **Route / endpoint definitions**: Express `app.get/post`, FastAPI `@router`, Spring `@RestController`/`@RequestMapping`, Django `urls.py`, ASP.NET `[Route]`, Go `http.HandleFunc`, Gin `r.GET`, gRPC `.proto` files
- **Database clients**: JDBC URLs, SQLAlchemy `create_engine`, Mongoose `connect`, Entity Framework `DbContext`, GORM `Open`, Diesel schema
- **Message queues / event streams**: Kafka producers/consumers, RabbitMQ channels, SQS/SNS client instantiation, Azure Service Bus, NATS
- **HTTP clients**: `axios`, `fetch`, `requests`, `HttpClient`, `RestTemplate`, `WebClient`, `urllib`, `curl` wrappers — especially where base URLs are configured
- **Auth / identity**: OIDC/OAuth config, `passport`, `spring-security`, `django-allauth`, Keycloak adapters, JWT validation, API key headers
- **Service discovery / config**: Consul, Eureka, etcd, Zookeeper client setup; environment-injected hostnames

#### Documentation & architecture hints
- `README.md`, `AGENTS.md`, `ARCHITECTURE.md`, `docs/`, `ADR/` directories
- OpenAPI / Swagger specs: `openapi.yaml`, `swagger.json`, `*.oas.yaml`
- AsyncAPI specs: `asyncapi.yaml`
- Architecture diagrams (even if informal): `*.drawio`, `*.puml`, `*.mermaid`

### Relationships — for each pair of nodes, identify:
- **connects** — direct point-to-point calls (HTTP, HTTPS, JDBC, gRPC, WebSocket, AMQP, TCP, etc.)
- **interacts** — actor-to-system interactions (user uses UI, external system calls API)
- **deployed-in** — containment (service runs in container, container runs in cluster)
- **composed-of** — logical composition (system made up of services)

## Output format

First, display a warning banner:

```⚠️ This is an initial discovery of potential CALM nodes and relationships based on static analysis of the codebase by an LLM. It may contain inaccuracies or omissions. Please review and validate each item before using it in your CALM architecture model.```

Produce two tables:

**Nodes table** with columns: `unique-id` | `node-type` | `name` | `description`

**Relationships table** with columns: `unique-id` | `relationship-type` | `protocol (if connects)` | `source → destination` | `description`

Then list key observations:
- Deployment boundaries
- Authentication / trust boundaries
- External dependencies
- Any nodes that likely have sub-architectures worth drilling into
