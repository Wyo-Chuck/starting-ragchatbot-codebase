# RAG System Query Flow Diagram

```
┌─────────────────┐    POST /api/query     ┌─────────────────┐
│   Frontend      │ ────────────────────> │   FastAPI       │
│  (script.js)    │ { query, session_id }  │   (app.py)      │
└─────────────────┘                       └─────────────────┘
                                                    │
                                                    │ query_documents()
                                                    ▼
                                          ┌─────────────────┐
                                          │   RAG System    │
                                          │ (rag_system.py) │
                                          └─────────────────┘
                                                    │
                                                    │ query()
                                                    ▼
┌─────────────────┐                       ┌─────────────────┐
│ Session Manager │ <──── get history ──── │  AI Generator   │
│(session_mgr.py) │                       │(ai_generator.py)│
└─────────────────┘                       └─────────────────┘
                                                    │
                                                    │ Claude API call
                                                    │ with tools
                                                    ▼
                                          ┌─────────────────┐
                                          │   Claude AI     │
                                          │ (Sonnet 4 API) │
                                          └─────────────────┘
                                                    │
                                                    │ function call:
                                                    │ search_courses()
                                                    ▼
┌─────────────────┐                       ┌─────────────────┐
│  Vector Store   │ <──── search ───────── │   Tool Manager  │
│(vector_store.py)│                       │(search_tools.py)│
└─────────────────┘                       └─────────────────┘
         │                                          │
         │ ChromaDB query                          │ return chunks
         ▼                                          │
┌─────────────────┐                                │
│    ChromaDB     │                                │
│  (vector db)    │                                │
└─────────────────┘                                │
         │                                          │
         │ relevant chunks                         │
         └──────────────────────────────────────────┘
                                                    │
                                                    │ chunks to Claude
                                                    ▼
                                          ┌─────────────────┐
                                          │   Claude AI     │
                                          │ generates final │
                                          │    response     │
                                          └─────────────────┘
                                                    │
                                                    │ response
                                                    ▼
                                          ┌─────────────────┐
                                          │   RAG System    │
                                          │ add to history  │
                                          └─────────────────┘
                                                    │
                                                    │ QueryResponse
                                                    ▼
┌─────────────────┐   { answer, sources } ┌─────────────────┐
│   Frontend      │ <──────────────────── │   FastAPI       │
│  (script.js)    │                       │   (app.py)      │
└─────────────────┘                       └─────────────────┘
```

## Flow Steps:

1. **Frontend** → FastAPI: User query via POST request
2. **FastAPI** → RAG System: Process query with session management
3. **RAG System** → AI Generator: Orchestrate query with conversation history
4. **AI Generator** → Claude API: Send query with available tools
5. **Claude** → Tool Manager: Function call to search courses
6. **Tool Manager** → Vector Store: Execute semantic search
7. **Vector Store** → ChromaDB: Query vector database
8. **ChromaDB** → Tool Manager: Return relevant chunks
9. **Tool Manager** → Claude: Provide search results
10. **Claude** → AI Generator: Generate contextualized response
11. **AI Generator** → RAG System: Return answer
12. **RAG System** → Session Manager: Update conversation history
13. **RAG System** → FastAPI: Return QueryResponse
14. **FastAPI** → Frontend: JSON response with answer and sources

## Key Components:
- **Function Calling**: Claude dynamically decides when/how to search
- **Semantic Search**: Vector embeddings for intelligent retrieval
- **Session State**: Conversation history maintained across queries
- **Tool Integration**: Modular search capabilities via tool manager