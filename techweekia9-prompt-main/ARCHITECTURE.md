# Prompt Engineering Project Architecture

## Overview

Interactive web application demonstrating different **prompt engineering techniques** by comparing results using the same model (GPT-4o-mini).

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Frontend (HTML/JS)                        │
│                   http://localhost:8000                      │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                    FastAPI Backend                           │
│                         :8000                                │
├─────────────────────────────────────────────────────────────┤
│  /api/review/*    │    /api/logic/*                        │
│  (Code Review)    │    (Logic Puzzles)                     │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                    OpenAI API                               │
│                   (GPT-4o-mini)                            │
└─────────────────────────────────────────────────────────────┘
```

## Directory Structure

```
├── main.py                    # FastAPI app + serve frontend
├── llm.py                     # OpenAI client
├── prompts.py                 # Prompts by technique
├── techniques.py              # Reasoning engines
├── routes/
│   ├── review.py             # Endpoints /api/review/*
│   └── logic.py              # Endpoints /api/logic/*
├── frontend.html             # Single-page interface
├── requirements.txt           # Python dependencies
├── docs/
│   └── setup.md             # Setup documentation
└── .env.example              # Environment template
```

## Implemented Techniques

### 1. Zero-shot
Direct instruction without structure. Useful for simple tasks.

### 2. Chain of Thought (CoT)
Forces the model to reason step by step before answering.

### 3. Tree of Thoughts (ToT)
Multiple reasoning paths explored in parallel:
- **Generate**: Creates candidates
- **Evaluate**: Evaluates each candidate
- **Expand**: Expands the best ones
- **Conclusion**: Selects the best answer

### 4. ReAct
Combines reasoning with real code execution:
- **Thought**: Model's reasoning
- **Action**: Tool call
- **Observation**: Action result

## Endpoints

### Code Review
| Method | Route | Description |
|--------|-------|-------------|
| GET | /api/review/simple | Zero-shot prompt |
| GET | /api/review/cot | Chain of Thought |
| GET | /api/review/tot | Tree of Thoughts |
| GET | /api/review/react | ReAct |

### Logic Puzzles
| Method | Route | Description |
|--------|-------|-------------|
| GET | /api/logic/simple | Zero-shot prompt |
| GET | /api/logic/cot | Chain of Thought |
| GET | /api/logic/tot | Tree of Thoughts |
| GET | /api/logic/react | ReAct |

## Execution Flow

1. **Frontend** loads HTML page
2. **User** clicks "Execute"
3. **JavaScript** makes parallel calls to all techniques
4. **Backend** processes each technique:
   - Zero-shot: Direct prompt
   - CoT: Adds "Let's think step by step"
   - ToT: Runs complete pipeline
   - ReAct: Loop with tools
5. **Responses** return as JSON
6. **Frontend** displays comparison grid

## Conventions

### Code
- Type hints on all functions
- Docstrings in English
- PEP 8

### Naming
- **Files**: snake_case
- **Functions**: snake_case
- **Constants**: UPPER_SNAKE_CASE

---

**Last Updated:** 30/03/2026
**Version:** 1.0.0
