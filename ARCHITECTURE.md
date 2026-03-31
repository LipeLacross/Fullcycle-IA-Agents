# Project Architecture

This document describes the architecture of the three projects in this repository, developed during the **Full Cycle TechWeek de IA #9**.

---

## Project Structure

```
Fullcycle-Ia-Agents/
├── techweekia9-multi-agents-rag-main/    # Multi-agent Legal RAG System
├── techweekia9-prompt-main/               # Prompt Engineering Demo
├── techweekia9-dev-com-ia-main/           # NestJS E-commerce API
├── README.md                              # Portuguese version
├── README_EN.md                           # English version
└── ARCHITECTURE.md                       # This file
```

---

# 1. Multi-Agents RAG System

## Project Structure

```
techweekia9-multi-agents-rag-main/
├── docker-compose.yml                     # Orchestration of 3 services
├── arquitetura-agentes.png                 # Architecture diagram
├── busca-semantica.png                     # Semantic search diagram
├── rag.png                                # RAG pipeline diagram
├── db/
│   └── init.sql                          # Schema: processes & process_chunks with pgvector
└── services/
    ├── agents/                            # Python Backend
    │   ├── agents/
    │   │   ├── root_agent.py             # Orchestrator agent (root)
    │   │   ├── pesquisa_agent.py        # Intra-process research agent
    │   │   ├── jurisprudencia_agent.py   # Jurisprudence agent
    │   │   └── analise_agent.py         # Strategic analysis agent
    │   ├── app/
    │   │   ├── config.py                 # Configuration & environment
    │   │   ├── db.py                     # PostgreSQL/pgvector queries
    │   │   └── server.py                 # FastAPI server endpoints
    │   ├── ingestion/
    │   │   ├── ingest.py                # Data ingestion script
    │   │   ├── embeddings.py             # Gemini embeddings generation
    │   │   └── mock_data.py              # Mock legal cases
    │   └── tools/
    │       ├── list_processes.py        # List available processes
    │       ├── select_process.py        # Select process for analysis
    │       ├── search_process.py        # Semantic search within process
    │       ├── search_similar.py        # Search jurisprudence
    │       └── get_process_context.py   # Get full process context
    └── web/                              # Next.js Frontend
        ├── app/
        │   ├── page.tsx                  # Main page
        │   ├── api/chat/route.ts        # API proxy to backend
        │   └── components/
        │       ├── Chat.tsx             # Chat component
        │       ├── MessageList.tsx      # Message list
        │       ├── MessageInput.tsx     # Message input
        │       └── ProcessSelector.tsx  # Process selector
        └── lib/
            └── api.ts                   # API types & utilities
```

## Architecture Principles

### Multi-Agent Orchestration
- **Google ADK** coordinates agent hierarchy
- **LiteLLM** abstracts LLM communication (Claude via Anthropic)
- Agents communicate via structured messages

### RAG Pipeline
- **Embedding**: Gemini Embedding 001 (768 dimensions)
- **Storage**: PostgreSQL + pgvector with HNSW index
- **Retrieval**: Cosine similarity search

### Separation of Concerns
- **Frontend**: Next.js handles UI/UX
- **Backend**: FastAPI exposes REST API
- **Database**: PostgreSQL stores documents and vectors
- **Agents**: Independent logic units with specific tools

---

# 2. Prompt Engineering Demo

## Project Structure

```
techweekia9-prompt-main/
├── main.py                               # FastAPI app + frontend server
├── llm.py                               # OpenAI client
├── prompts.py                           # Prompts organized by technique
├── techniques.py                        # Reasoning engines (CoT, ToT, ReAct)
├── routes/
│   ├── review.py                        # Code review endpoints
│   └── logic.py                         # Logic puzzle endpoints
├── frontend.html                        # Single-page comparison interface
├── requirements.txt                     # Python dependencies
├── docs/
│   └── setup.md                        # Setup documentation
└── .env.example                        # Environment template
```

## Architecture Principles

### Technique Implementations
- **Zero-shot**: Direct instructions without structure
- **Chain of Thought (CoT)**: Step-by-step reasoning
- **Tree of Thoughts (ToT)**: Multiple reasoning branches
- **ReAct**: Reasoning + action execution

### Single-Page Comparison
- JavaScript executes all techniques in parallel
- Results displayed in side-by-side grid

---

# 3. FullCycle Mercadinho (E-commerce)

## Project Structure

```
techweekia9-dev-com-ia-main/
├── CLAUDE.md                           # AI agent guidelines
├── compose.yaml                        # Docker orchestration
├── Dockerfile.dev                      # Development container
├── docs/
│   └── PRD.md                         # Product requirements
├── src/
│   ├── main.ts                        # Application entry point
│   ├── app.module.ts                  # Root module
│   ├── app.controller.ts              # Root controller
│   ├── app.service.ts                 # Root service
│   └── *.module.ts, *.controller.ts, *.service.ts  # Feature modules
├── test/
│   ├── app.e2e-spec.ts                # E2E tests
│   └── jest-e2e.json                 # Jest config
└── package.json
```

## Architecture Principles

### Layered Architecture (MVC)
```
Controller (HTTP) → Service (Business Logic) → Repository (Data)
```

### Design Patterns
- **Controllers**: Thin HTTP layer - parsing, validation, status codes
- **Services**: Pure business logic, HTTP-agnostic
- **Repositories**: TypeORM entities and data access

### AI-Assisted Development
- **CLAUDE.md**: Guidelines for coding agents
- **Skills**: Modular rules for specific contexts
- **Git Worktree**: Parallel development with multiple agents

---

# Conventions

## Naming Conventions
- **Files**: kebab-case (e.g., `process-selector.tsx`)
- **Classes**: PascalCase (e.g., `ChatComponent`)
- **Functions/variables**: camelCase (e.g., `getProcessList`)
- **Constants**: UPPER_SNAKE_CASE

## Code Style
- **Python**: PEP 8, type hints
- **TypeScript**: ESLint + Prettier
- **Frontend**: TailwindCSS for styling

## Database
- **PostgreSQL**: Primary database
- **pgvector**: Vector similarity search
- **TypeORM**: ORM for NestJS project

---

# Maintenance and Expansion

## Adding New Features

### Multi-Agents RAG
1. Create new agent in `services/agents/agents/`
2. Define tools in `services/agents/tools/`
3. Register agent in root orchestrator
4. Add API endpoint in `services/agents/app/server.py`

### Prompt Engineering
1. Add technique in `techniques.py`
2. Create prompts in `prompts.py`
3. Add endpoint in `routes/`
4. Update frontend comparison grid

### E-commerce
1. Create module: `src/<feature>.module.ts`
2. Add controller, service, repository
3. Generate TypeORM migration
4. Write unit and E2E tests

## Updating Dependencies

```bash
# Python projects
pip install -r requirements.txt

# Node projects
npm install
```

## Testing

```bash
# Unit tests (NestJS)
docker compose exec nestjs-api npm test

# E2E tests
docker compose exec nestjs-api npm run test:e2e
```

---

**Last Updated:** 30/03/2026
**Version:** 1.0.0
**Maintainer:** Felipe Moreira
