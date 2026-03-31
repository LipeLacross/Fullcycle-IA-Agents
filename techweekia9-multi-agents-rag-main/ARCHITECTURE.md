# Multi-Agents RAG System Architecture

## Overview

Multi-agent system for analyzing legal cases using **RAG (Retrieval-Augmented Generation)** with vector semantic search. Built with **Google ADK** and **LiteLLM** for communication with Anthropic's API.

## System Architecture

### Main Components

```
┌─────────────────────────────────────────────────────────────────┐
│                      Docker Compose                              │
├─────────────────┬─────────────────────┬─────────────────────────┤
│     Frontend    │       Agents        │       Database          │
│   (Next.js)     │    (FastAPI)       │  (PostgreSQL + pgvector)│
│    :3000        │     :8000          │        :5432            │
└────────┬────────┴────────┬───────────┴────────────┬────────────┘
         │                 │                        │
         │                 │                        │
         │        ┌────────▼────────┐               │
         │        │   Google ADK    │               │
         │        │    Orchestrator │               │
         │        └────────┬────────┘               │
         │                 │                        │
         │    ┌────────────┼────────────┐           │
         │    │            │            │           │
         │    ▼            ▼            ▼           │
         │ ┌──────┐  ┌──────────┐  ┌─────────┐    │
         │ │Research│  │Jurisprud-│  │ Analysis│    │
         │ │ Agent │  │  ence    │  │  Agent  │    │
         │ └──────┘  └──────────┘  └─────────┘    │
         │                                           │
         └───────────────────────────────────────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │ LiteLLM → Claude│
                 │    (Anthropic)  │
                 └─────────────────┘
```

## Directory Structure

```
services/agents/
├── agents/                      # Agent definitions
│   ├── root_agent.py           # Orchestrator agent
│   ├── pesquisa_agent.py       # Research agent
│   ├── jurisprudencia_agent.py # Jurisprudence agent
│   └── analise_agent.py        # Analysis agent
├── app/
│   ├── config.py               # Configuration
│   ├── db.py                   # PostgreSQL connection
│   └── server.py               # FastAPI server
├── ingestion/
│   ├── ingest.py               # Ingestion script
│   ├── embeddings.py           # Embeddings generation
│   └── mock_data.py            # Mock data
└── tools/
    ├── list_processes.py       # List processes
    ├── select_process.py       # Select process
    ├── search_process.py       # Semantic search
    ├── search_similar.py       # Jurisprudence search
    └── get_process_context.py  # Process context
```

## RAG Pipeline

### 1. Data Ingestion

1. **Input**: Legal documents (initial petition, defense, testimonies, judgment)
2. **Chunking**: Split into chunks with metadata (section, page)
3. **Embedding**: Convert to 768D vectors via Gemini Embedding 001
4. **Storage**: PostgreSQL with pgvector and HNSW index

### 2. Semantic Search

1. **Query embedding**: Question converted to vector
2. **Similarity search**: Cosine between query and chunks
3. **Retrieval**: Return most relevant chunks

### 3. Response Generation

1. **Context injection**: Relevant chunks injected into prompt
2. **LLM generation**: Claude Sonnet 4 generates response

## Agents

### Orchestrator (root_agent)
- Coordinates entire system
- Manages session and state
- Delegates tasks to specialized agents

### Research Agent
- Searches information within current process
- Uses RAG to find relevant passages
- Cites section and page

### Jurisprudence Agent
- Searches similar cases in other processes
- Identifies precedents and similar rulings
- Foundation in jurisprudence

### Analysis Agent
- Generates strategic action plan
- Analyzes strengths/weaknesses
- Next steps with foundation

## Database

### Schema

```sql
-- Processes
CREATE TABLE processes (
    id UUID PRIMARY KEY,
    name VARCHAR(255),
    area VARCHAR(100),
    description TEXT
);

-- Chunks with embeddings
CREATE TABLE process_chunks (
    id SERIAL PRIMARY KEY,
    process_id UUID REFERENCES processes(id),
    content TEXT,
    section VARCHAR(100),
    page INTEGER,
    embedding vector(768)
);

-- HNSW index for efficient search
CREATE INDEX ON process_chunks 
USING hnsw (embedding vector_cosine_ops);
```

## API Endpoints

| Method | Route | Description |
|--------|-------|-------------|
| POST | /chat | Send message to system |
| GET | /processes | List processes |
| POST | /ingest | Ingest mock data |

## Conventions

### Naming
- **Agents**: snake_case (e.g., `pesquisa_agent`)
- **Functions**: snake_case (e.g., `list_processes`)
- **Tables**: snake_case (e.g., `process_chunks`)
- **Fields**: snake_case

### Code
- Type hints on all functions
- Docstrings in English
- Pydantic models for validation

## Maintenance

### Adding new agent
1. Create file in `agents/`
2. Define tools in `tools/`
3. Register in root_agent

### Updating embedding
1. Modify `ingestion/embeddings.py`
2. Reingest data: `python -m ingestion.ingest`

---

**Last Updated:** 30/03/2026
**Version:** 1.0.0
