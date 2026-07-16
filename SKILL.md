---
name: kratos-skills
description: Build, review, debug, and modernize Go microservices based on go-kratos v2. Use when Codex works on Kratos projects involving Protobuf HTTP/gRPC APIs, Service-Biz-Data layering, Wire dependency injection, configuration, middleware, validation, structured errors, registry and discovery, resilience, observability, Ent, CLI or code generation, production hardening, or Kratos-specific troubleshooting.
---

# Go-Kratos Engineering

Use the repository as the source of truth, then apply the Kratos patterns in this skill selectively.

## Work From the Codebase

1. Inspect `go.mod`, `Makefile`, `buf.yaml`, `buf.gen.yaml`, API protos, `cmd/`, and `internal/` before proposing changes.
2. Identify the exact Kratos, Go, Protobuf, Wire, validation, and contrib package versions already in use.
3. Follow the project's existing generation commands, provider-set structure, naming, and error conventions.
4. Load only the reference files required for the current task.
5. Implement the smallest coherent change across API, service, biz, data, configuration, and wiring boundaries.
6. Regenerate derived code and run focused tests; broaden verification when shared contracts or infrastructure change.

Do not copy commands or APIs from a reference blindly. Reconcile every example with the versions and conventions found in the target repository.

## Preserve Kratos Boundaries

- Keep transport adaptation in `internal/service`; translate generated request and response types there.
- Keep business rules and repository interfaces in `internal/biz`.
- Keep persistence and external-system implementations in `internal/data`.
- Compose dependencies with Wire provider sets; avoid globals and service locators.
- Define public APIs, validation rules, and structured error contracts in Protobuf when the project does so already.
- Propagate `context.Context`, deadlines, metadata, and tracing through every layer and outbound call.
- Treat generated `.pb.go`, HTTP, gRPC, error, validation, and `wire_gen.go` files as derived output. Change their source definitions instead of editing generated files.

Adapt these defaults when the repository intentionally uses a different architecture. Preserve local consistency unless the user explicitly requests a migration.

## Route to References

Select the narrowest useful row. Read each selected file completely before implementing its patterns.

| Task | Read |
| --- | --- |
| Create or reshape a service, use case, repository, or Wire graph | [references/architecture-patterns.md](references/architecture-patterns.md) |
| Define Protobuf APIs or HTTP mappings | [references/api-patterns.md](references/api-patterns.md) |
| Configure HTTP/gRPC servers or clients | [references/transport-patterns.md](references/transport-patterns.md) |
| Use Kratos CLI or project generators | [references/cli-guide.md](references/cli-guide.md) |
| Define and propagate structured errors | [references/error-patterns.md](references/error-patterns.md) |
| Add Protovalidate or migrate validation | [references/validate-patterns.md](references/validate-patterns.md) |
| Add middleware or reason about middleware order | [references/middleware-patterns.md](references/middleware-patterns.md) |
| Add JWT authentication or authorization context | [references/auth-patterns.md](references/auth-patterns.md) |
| Configure files, environment variables, or config centers | [references/config-patterns.md](references/config-patterns.md) |
| Add registry, discovery, or client-side balancing | [references/registry-patterns.md](references/registry-patterns.md), [references/selector-patterns.md](references/selector-patterns.md) |
| Add circuit breaking, rate limiting, or panic recovery | [references/circuit-breaker-patterns.md](references/circuit-breaker-patterns.md), [references/ratelimit-patterns.md](references/ratelimit-patterns.md), [references/recovery-patterns.md](references/recovery-patterns.md) |
| Add logs, metrics, traces, or propagated metadata | [references/logging-patterns.md](references/logging-patterns.md), [references/metrics-patterns.md](references/metrics-patterns.md), [references/tracing-patterns.md](references/tracing-patterns.md), [references/metadata-patterns.md](references/metadata-patterns.md) |
| Integrate Ent or implement Ent repositories and transactions | [references/ent-patterns.md](references/ent-patterns.md) |
| Customize serialization or content negotiation | [references/encoding-patterns.md](references/encoding-patterns.md) |
| Generate or serve OpenAPI documentation | [references/openapi-guide.md](references/openapi-guide.md) |
| Diagnose installation, generation, Wire, build, runtime, database, or discovery failures | [troubleshooting/common-issues.md](troubleshooting/common-issues.md) plus the relevant topic reference |
| Perform a broad production-readiness or code-quality review | [best-practices/overview.md](best-practices/overview.md) plus references for the affected subsystems |
| Configure or invoke this skill from Claude Code | [getting-started/claude-code-guide.md](getting-started/claude-code-guide.md) |

For broad requests, start with architecture or best practices and add subsystem references only when the code under inspection requires them. Do not load the entire knowledge base at once.

## Apply Task-Specific Checks

### API and Generation

- Confirm `go_package`, package versioning, HTTP annotations, request bodies, path parameters, and generated targets.
- Preserve backward compatibility unless the user approves a breaking contract change.
- Use repository-owned Make or Buf targets when available instead of inventing a parallel generation command.
- Include regenerated outputs only when the repository tracks them.

### Layered Implementation

- Keep service methods thin and delegate orchestration to use cases.
- Define repository behavior from business needs, not storage technology.
- Map storage and transport errors into the project's domain error model at the appropriate boundary.
- Update Wire providers whenever constructor dependencies or implementations change.

### Infrastructure and Reliability

- Set explicit timeouts for outbound calls and preserve cancellation.
- Apply middleware on the correct client or server side and verify ordering from actual behavior.
- Avoid retrying non-idempotent work without an idempotency strategy.
- Bound retries, rate limits, circuit breakers, connection pools, and queues with observable failure behavior.
- Avoid logging secrets, credentials, tokens, or full sensitive request payloads.

### Review and Debugging

- Reproduce or trace the failing path before changing architecture.
- Check generated-code drift, missing tools, stale Wire graphs, configuration source precedence, discovery endpoints, and context deadlines.
- In reviews, prioritize correctness, contract compatibility, layer violations, error handling, cancellation, security, observability, and missing tests.
- Cite concrete files and lines when reporting findings.

## Verify Changes

Prefer commands already defined by the project. A typical verification sequence is:

1. Run the relevant Proto, config, error, Ent, or Wire generation target.
2. Run `gofmt` on handwritten Go files.
3. Run focused package tests for the changed layers.
4. Run `go test ./...` when shared APIs, wiring, middleware, or infrastructure behavior changes.
5. Run `go vet ./...` or the repository linter when available and proportionate to the change.
6. Inspect `git diff` to ensure generated output and configuration changes are intentional.

If a required generator, service, or dependency is unavailable, report exactly what could not be verified and provide the command that should be run in the configured environment.
