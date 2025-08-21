# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Running the Application
```bash
# Quick start using provided script
./run.sh

# Manual start
cd backend && uv run uvicorn app:app --reload --port 8000
```

### Package Management
```bash
# Install dependencies
uv sync

# The project uses uv for Python package management (not pip/poetry)
```

### Environment Setup
- Create `.env` file with `ANTHROPIC_API_KEY=your_key_here`
- Requires Python 3.13+

## Architecture Overview

This is a **Retrieval-Augmented Generation (RAG) system** for querying course materials with the following key architecture:

### Core Components (backend/)
- **app.py**: FastAPI web server with API endpoints (`/api/query`, `/api/courses`) and static file serving
- **rag_system.py**: Main orchestrator that coordinates all RAG operations
- **vector_store.py**: ChromaDB interface for semantic search and document storage
- **ai_generator.py**: Anthropic Claude integration for AI responses
- **document_processor.py**: Text chunking and course document parsing
- **session_manager.py**: Conversation history management
- **search_tools.py**: Tool-based search system for Claude function calling
- **models.py**: Data models for Course, Lesson, CourseChunk entities
- **config.py**: Configuration management with environment variables

### Frontend (frontend/)
- Static HTML/CSS/JS web interface served by FastAPI

### Data Flow
1. Course documents (in `docs/` folder) are processed into chunks on startup
2. Chunks are stored in ChromaDB with semantic embeddings
3. User queries trigger tool-based search through Claude function calling
4. RAG system retrieves relevant chunks and generates contextualized responses
5. Conversation history is maintained per session

### Key Configuration (backend/config.py)
- Uses `claude-sonnet-4-20250514` model
- `all-MiniLM-L6-v2` for embeddings
- 800 character chunks with 100 character overlap
- ChromaDB stored in `./chroma_db`

### API Endpoints
- `POST /api/query`: Process queries with RAG
- `GET /api/courses`: Get course statistics
- Web interface at `http://localhost:8000`
- API docs at `http://localhost:8000/docs`

## Important Notes

- No test suite exists in this codebase
- Documents are loaded automatically from `docs/` folder on startup
- Uses FastAPI with uvicorn for the web server
- Vector store handles duplicate course detection to avoid re-processing
- Session-based conversation history with configurable message limits