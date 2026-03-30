# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**FullCycle Mercadinho** — a B2C e-commerce API built with NestJS 11 and TypeScript. The product is an online retail store with Stripe payments, targeting the Brazilian market (BRL currency). See `docs/PRD.md` for the full product requirements document.

## Commands

**All project and database commands MUST be executed inside the Docker containers.** Never run `npm`, `npx`, `node`, or `psql` commands directly on the host.

```bash
# Execute commands inside the API container
docker compose exec nestjs-api <command>

# Install dependencies
docker compose exec nestjs-api npm install

# Development (watch mode)
docker compose exec nestjs-api npm run start:dev

# Build
docker compose exec nestjs-api npm run build

# Lint (auto-fix)
docker compose exec nestjs-api npm run lint

# Format
docker compose exec nestjs-api npm run format

# Unit tests (rootDir is src/, matches *.spec.ts)
docker compose exec nestjs-api npm test

# Run a single unit test
docker compose exec nestjs-api npx jest --testPathPattern=<pattern>

# E2E tests (config at test/jest-e2e.json, matches *.e2e-spec.ts)
docker compose exec nestjs-api npm run test:e2e

# Test coverage
docker compose exec nestjs-api npm run test:cov

# TypeORM migrations
docker compose exec nestjs-api npx typeorm migration:generate src/migrations/<MigrationName> -d src/data-source.ts
docker compose exec nestjs-api npx typeorm migration:run -d src/data-source.ts

# PostgreSQL access
docker compose exec db psql -U fullcycle -d fullcycle
```

## Docker Development

The project uses Docker Compose (`compose.yaml`) with two services:
- **nestjs-api**: Node 25.6, mounts the project directory into `/home/node/app`, exposed on port 3000
- **db**: PostgreSQL 16 (database: `fullcycle`, user: `fullcycle`, password: `fullcycle`)

The dev container (`Dockerfile.dev`) runs as the `node` user and keeps the container alive with `tail -f /dev/null` — you exec into it to run commands.

## Architecture

### Layered Architecture (MVC)

Standard NestJS scaffold. Entry point is `src/main.ts`, listening on `process.env.PORT` or 3000. The app module (`src/app.module.ts`) registers controllers and providers.

The project follows strict layer separation:

- **Controllers** are thin HTTP intermediaries. They handle request parsing, input validation (via DTOs/pipes), HTTP status codes, and response shaping. They delegate all business logic to services. Controllers are the only layer that knows about HTTP (request, response, headers, status codes).
- **Services** contain all business logic and are HTTP-agnostic. A service **must not** import or use any HTTP concept — no `Request`/`Response` objects, no `HttpException` or HTTP status codes, no header manipulation. Services throw domain-specific errors (custom exception classes) that controllers or exception filters map to HTTP responses. This ensures services are reusable by any client (CLI, queue worker, gRPC, etc.).
- **Repositories / Data Access** — TypeORM entities and repositories handle persistence.

### Design Patterns

When implementing classes, watch for repetitive patterns and verbose code. Apply design patterns (Strategy, Factory, Builder, etc.) where they reduce duplication and improve clarity. Avoid premature abstraction — apply patterns only when the complexity justifies them.

TypeScript is configured with `nodenext` module resolution, ES2023 target, and decorator metadata enabled (`emitDecoratorMetadata`/`experimentalDecorators`). The build excludes test files via `tsconfig.build.json`.

## REST API Best Practices

This is a REST API. Every endpoint must follow these conventions:

- **URL design**: Use plural nouns for resources (`/products`, `/orders`). Nest sub-resources logically (`/orders/:id/items`). Avoid verbs in URLs.
- **HTTP methods**: `GET` (read), `POST` (create), `PUT` (full replace), `PATCH` (partial update), `DELETE` (remove). Use them semantically.
- **Status codes**: Use precise codes — `201 Created` for POST success, `204 No Content` for DELETE, `400 Bad Request` for validation errors, `404 Not Found`, `409 Conflict`, `422 Unprocessable Entity`, etc. Never return `200` for everything.
- **Input validation**: All incoming data must be validated using class-validator DTOs with NestJS `ValidationPipe`. Never trust raw input.
- **Error responses**: Return consistent error payloads with `statusCode`, `message`, and `error` fields. Use NestJS exception filters for centralized error handling.
- **Headers**: Set appropriate `Content-Type`, handle `Accept` headers. Use pagination headers or envelope when listing resources.
- **Pagination**: List endpoints must support pagination (`limit`/`offset` or cursor-based).

## Database & Migrations

The project uses TypeORM with PostgreSQL. Although `synchronize: true` is enabled for convenience during development, **every schema change that introduces or modifies entities must also have a corresponding migration file**. This ensures the schema can be reproduced reliably in any environment.

Workflow:
1. Create/modify the TypeORM entity.
2. Generate a migration: `docker compose exec nestjs-api npx typeorm migration:generate src/migrations/<Name> -d src/data-source.ts`
3. Review the generated migration.
4. Run it: `docker compose exec nestjs-api npx typeorm migration:run -d src/data-source.ts`

## Testing Strategy

Every feature must include tests. Follow this testing pyramid:

### Unit Tests (`*.spec.ts`)
- **Controllers**: Test that they delegate correctly to services, validate inputs, and return proper HTTP responses/status codes. Mock the service layer.
- **Services**: Test business logic in isolation. Mock repositories and external dependencies.
- **Other structures** (guards, pipes, interceptors, etc.): Test loading and behavior.
- Unit tests validate module loading and dependency injection wiring — always verify that the module compiles and services resolve.

### Integration Tests
- For any artifact that depends on external infrastructure (database, file system, etc.), use **integration tests with the real tool** running inside Docker. Do not mock the database — use an actual PostgreSQL instance.
- Exception: external HTTP services (third-party APIs) may be mocked when calling them directly in tests is impractical or costly.

### E2E Tests (`*.e2e-spec.ts`)
- Always evaluate the need for E2E tests to validate end-to-end behavior through the HTTP layer.
- E2E tests exercise the full request lifecycle: HTTP request → controller → service → database → response.
- Run these inside the Docker container against real infrastructure.

## Documentation Validation

Use the **context7** MCP tool to look up and validate NestJS, TypeORM, class-validator, and other library APIs before implementing structures. This ensures implementations follow current library conventions and avoid deprecated patterns.

## Code Style

- **ESLint**: Flat config (`eslint.config.mjs`) with `typescript-eslint` recommended + type-checked rules, Prettier integration. `no-explicit-any` is off; `no-floating-promises` and `no-unsafe-argument` are warnings.
- **Prettier**: Single quotes, trailing commas everywhere, auto line endings.