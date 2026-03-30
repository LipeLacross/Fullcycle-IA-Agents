# FullCycle Mercadinho

API de e-commerce B2C construída com **NestJS 11** e **TypeScript**.

Este projeto foi desenvolvido durante a **TechWeek de IA da Full Cycle** como demonstração prática de desenvolvimento assistido por agentes de IA (coding agents). O foco não é apenas o produto final, mas o **processo**: como estruturar regras para a IA, criar planejamentos, e usar técnicas como `git worktree` para desenvolver features em paralelo com agentes independentes.

> Consulte o [PRD completo](docs/PRD.md) para os requisitos detalhados do produto.

---

## Branches e Fases do Projeto

O projeto está organizado em branches que representam as diferentes fases de desenvolvimento e as técnicas de IA utilizadas:

```
main
 ├── feature/auth     ← Fase 0 (Catálogo) + Fase 1 (Autenticação)
 ├── feature/cart      ← Fase 2 (Carrinho) — via git worktree + IA
 └── feature/email     ← Fase 5 (Notificações por Email) — via git worktree + IA
```

| Fase | Funcionalidade | Branch | Seção do PRD |
|------|---------------|--------|-------------|
| 0 | Catálogo de Produtos | `feature/auth` | 4.1 |
| 1 | Autenticação (Conta do Cliente) | `feature/auth` | 4.2 |
| 2 | Carrinho de Compras | `feature/cart` | 4.3 |
| 5 | Notificações por Email | `feature/email` | 4.7 |

### `main` — Setup inicial

Contém a estrutura base do projeto NestJS e o arquivo `CLAUDE.md` com as guidelines iniciais para o agente de IA. Este arquivo funciona como um "manual de instruções" que orienta o coding agent sobre arquitetura, padrões REST, convenções de código e como rodar o projeto.

### `feature/auth` — Fase 0 (Catálogo) + Fase 1 (Autenticação)

Implementação completa do **catálogo de produtos** (listagem, categorias, busca, filtros, paginação) e do sistema de **autenticação** (cadastro, login, JWT).

Além do código, esta branch contém a evolução das ferramentas de IA:

- **Subdivisão das Claude rules** — O `CLAUDE.md` monolítico foi dividido em regras modulares para contextos específicos, permitindo que o agente receba instruções mais focadas dependendo da tarefa.
- **Skill de boas práticas NestJS** — Skill customizada com padrões e convenções específicos do NestJS para guiar a geração de código.
- **`prompt.md`** — Arquivo documentando todos os prompts utilizados: criação das rules iniciais, melhorias iterativas do `CLAUDE.md`, e criação de um planejamento estruturado para as fases de desenvolvimento.

### `feature/cart` — Fase 2 (Carrinho de Compras)

Implementação do **carrinho de compras**: adicionar/remover itens, persistência de sessão, validação de estoque e migração do carrinho ao fazer login.

Desenvolvida **em paralelo** com a branch `feature/email` usando **`git worktree`**, permitindo que dois agentes de IA trabalhassem simultaneamente em features independentes, cada um com seu próprio diretório de trabalho.

### `feature/email` — Fase 5 (Notificações por Email)

Implementação das **notificações por email**: confirmação de cadastro, confirmação de pedido, atualização de status e recuperação de senha.

Também desenvolvida **em paralelo** via `git worktree` + IA, demonstrando como escalar o desenvolvimento com múltiplos agentes trabalhando ao mesmo tempo sem conflitos de branch.

---

## Desenvolvimento com IA

Este projeto demonstra um workflow de desenvolvimento assistido por IA com as seguintes técnicas:

### 1. Claude Rules (`CLAUDE.md`)

O arquivo `CLAUDE.md` na raiz do projeto funciona como um contrato entre o desenvolvedor e o agente de IA. Ele define:
- Comandos de desenvolvimento (sempre via Docker)
- Arquitetura em camadas e separação de responsabilidades
- Convenções REST (URLs, métodos, status codes)
- Estratégia de testes (pirâmide de testes)
- Padrões de banco de dados e migrations

### 2. Subdivisão de Rules (branch `feature/auth`)

Conforme o projeto cresceu, o `CLAUDE.md` monolítico foi dividido em arquivos modulares. Isso permite que o agente receba apenas as instruções relevantes para a tarefa atual, reduzindo ruído e melhorando a qualidade do código gerado.

### 3. Skills Customizadas

Uma skill de boas práticas do NestJS foi criada para padronizar a geração de código — garantindo que módulos, controllers, services, DTOs e testes sigam as convenções do framework.

### 4. Prompt Engineering (`prompt.md`)

O arquivo `prompt.md` documenta os prompts utilizados ao longo do desenvolvimento:
- Criação das rules iniciais
- Melhorias iterativas das rules
- Melhoria do `CLAUDE.md`
- Criação de planejamento estruturado para implementação das fases

### 5. Git Worktree para Desenvolvimento Paralelo

Usando `git worktree`, foi possível criar diretórios de trabalho independentes para branches diferentes. Isso permitiu que **dois agentes de IA** trabalhassem **simultaneamente** em features distintas (`feature/cart` e `feature/email`), sem conflitos, maximizando a produtividade.

```bash
# Exemplo: criando worktrees para desenvolvimento paralelo
git worktree add ../mercadinho-cart feature/cart
git worktree add ../mercadinho-email feature/email

# Cada diretório tem seu próprio agente de IA trabalhando independentemente
```

---

## Pré-requisitos

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

Não é necessário ter Node.js instalado localmente — tudo roda dentro dos containers.

---

## Quick Start

```bash
# 1. Subir os containers (API + PostgreSQL)
docker compose up -d

# 2. Instalar dependências
docker compose exec nestjs-api npm install

# 3. Rodar em modo de desenvolvimento (watch mode)
docker compose exec nestjs-api npm run start:dev
```

A API estará disponível em `http://localhost:3000`.

---

## Comandos

> **Importante:** Todos os comandos devem ser executados dentro do container Docker. Nunca rode `npm`, `npx` ou `node` diretamente no host.

### Desenvolvimento

```bash
# Watch mode
docker compose exec nestjs-api npm run start:dev

# Build de produção
docker compose exec nestjs-api npm run build

# Lint (com auto-fix)
docker compose exec nestjs-api npm run lint

# Formatação (Prettier)
docker compose exec nestjs-api npm run format
```

### Testes

```bash
# Testes unitários
docker compose exec nestjs-api npm test

# Rodar um teste específico
docker compose exec nestjs-api npx jest --testPathPattern=<pattern>

# Testes E2E
docker compose exec nestjs-api npm run test:e2e

# Cobertura de testes
docker compose exec nestjs-api npm run test:cov
```

### Banco de Dados

```bash
# Gerar migration
docker compose exec nestjs-api npx typeorm migration:generate src/migrations/<NomeDaMigration> -d src/data-source.ts

# Rodar migrations
docker compose exec nestjs-api npx typeorm migration:run -d src/data-source.ts

# Acessar o PostgreSQL
docker compose exec db psql -U fullcycle -d fullcycle
```

---

## Arquitetura

O projeto segue uma **arquitetura em camadas (MVC)** com separação estrita de responsabilidades:

```
Controller (HTTP) → Service (Lógica de Negócio) → Repository (Dados)
```

- **Controllers** — Camada fina de HTTP: parsing de request, validação via DTOs, status codes e formatação de response. Delega toda lógica para services.
- **Services** — Lógica de negócio pura, sem dependência de HTTP. Lançam erros de domínio (nunca `HttpException`). Reutilizáveis por qualquer interface (CLI, filas, gRPC).
- **Repositories** — Entidades TypeORM e acesso a dados com PostgreSQL.

### Stack

| Tecnologia | Uso |
|------------|-----|
| **NestJS 11** | Framework da API |
| **TypeScript** | Linguagem |
| **TypeORM** | ORM e migrations |
| **PostgreSQL 16** | Banco de dados |
| **Docker** | Ambiente de desenvolvimento |
| **Stripe** | Pagamentos |
| **Jest** | Testes (unitários e E2E) |
| **ESLint + Prettier** | Qualidade e formatação de código |

---

## Estrutura do Projeto

```
├── CLAUDE.md              # Guidelines para o agente de IA
├── compose.yaml           # Docker Compose (API + PostgreSQL)
├── Dockerfile.dev         # Container de desenvolvimento
├── docs/
│   └── PRD.md             # Documento de requisitos do produto
├── src/
│   ├── main.ts            # Entry point da aplicação
│   ├── app.module.ts      # Módulo raiz
│   ├── app.controller.ts  # Controller raiz
│   ├── app.service.ts     # Service raiz
│   └── app.controller.spec.ts  # Teste do controller
├── test/
│   ├── app.e2e-spec.ts    # Teste E2E
│   └── jest-e2e.json      # Config do Jest E2E
├── package.json
├── tsconfig.json
└── eslint.config.mjs
```

> As branches `feature/*` contêm módulos adicionais (products, auth, cart, email, etc.) dentro de `src/`.

---

## Licença

Este projeto é parte do material educacional da [Full Cycle](https://fullcycle.com.br).
