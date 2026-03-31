# FullCycle Mercadinho Project Architecture

## Overview

B2C e-commerce API built with **NestJS 11** and **TypeScript**. The project demonstrates AI-assisted development with a focus on layered architecture and REST best practices.

## System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      Docker Compose                              │
├──────────────────────────┬──────────────────────────────────────┤
│     API (NestJS)        │         Database                      │
│       :3000             │         (PostgreSQL :5432)           │
└───────────┬──────────────┴──────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────────────┐
│                    MVC Layered Architecture                     │
├─────────────────────────────────────────────────────────────────┤
│  Controller → Service → Repository                              │
│     (HTTP)     (Business)    (Data)                             │
└─────────────────────────────────────────────────────────────────┘
```

## Directory Structure

```
├── CLAUDE.md                      # AI agent guidelines
├── compose.yaml                   # Docker orchestration
├── Dockerfile.dev                 # Development container
├── docs/
│   └── PRD.md                    # Product requirements
├── src/
│   ├── main.ts                   # Entry point
│   ├── app.module.ts             # Root module
│   ├── app.controller.ts         # Root controller
│   ├── app.service.ts            # Root service
│   └── *.module.ts, *.controller.ts, *.service.ts  # Additional modules
├── test/
│   ├── app.e2e-spec.ts           # E2E tests
│   └── jest-e2e.json             # Jest configuration
└── package.json
```

## Layered Architecture

### Controller Layer
- Thin HTTP layer
- Request parsing
- DTO validation
- HTTP status codes
- Response formatting
- **Responsibility**: HTTP intermediary only

### Service Layer
- Pure business logic
- HTTP-agnostic
- No HttpException
- Reusable by any client (CLI, queues, gRPC)
- **Responsibility**: Business rules

### Repository Layer
- TypeORM entities
- Data access
- PostgreSQL
- **Responsibility**: Persistence

## Tech Stack

| Technology | Usage |
|------------|-----|
| NestJS 11 | API Framework |
| TypeScript | Language |
| TypeORM | ORM and migrations |
| PostgreSQL 16 | Database |
| Docker | Development environment |
| Stripe | Payments |
| Jest | Unit and E2E tests |
| ESLint + Prettier | Code quality |

## Conventions

### REST API
- **URLs**: Plural for resources (`/products`, `/orders`)
- **HTTP Methods**: GET, POST, PUT, PATCH, DELETE
- **Status Codes**: 201 Created, 204 No Content, 400 Bad Request, 404 Not Found, 422 Unprocessable Entity
- **Validation**: class-validator with ValidationPipe

### Code
- **ESLint**: Flat config with typescript-eslint
- **Prettier**: Single quotes, trailing commas
- **TypeScript**: nodenext module resolution

### Naming
- **Files**: kebab-case (e.g., `process-selector.ts`)
- **Classes**: PascalCase (e.g., `ProductService`)
- **Functions/variables**: camelCase (e.g., `getProductList`)
- **Constants**: UPPER_SNAKE_CASE

## AI-Assisted Development

### CLAUDE.md
Guidelines file for AI agents that defines:
- Development commands (always via Docker)
- Layered architecture
- REST conventions
- Testing strategy

### Git Worktree
Parallel development with multiple agents:
```bash
git worktree add ../mercadinho-cart feature/cart
git worktree add ../mercadinho-email feature/email
```

## Testing

### Unit Tests (`*.spec.ts`)
- Controllers: Delegation to services
- Services: Isolated business logic
- Repositories: Dependency mocking

### E2E Tests (`*.e2e-spec.ts`)
- Complete HTTP request testing
- Real database in Docker

## Database

### Migrations
```bash
# Generate migration
npx typeorm migration:generate src/migrations/<Name>

# Run migration
npx typeorm migration:run
```

---

**Last Updated:** 30/03/2026
**Version:** 1.0.0
