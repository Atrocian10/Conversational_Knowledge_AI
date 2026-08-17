# Conversational Knowledge Navigator

A real-time, voice-based knowledge assistant built with LangGraph that orchestrates retrieval, memory, reasoning, and tool execution as a single stateful agentic workflow over streaming speech.

> **Authored by Shashwat Tripathi, IIT Bombay**

## Features

- 🎙️ **Voice-Driven Interface**: Real-time streaming speech input with stateful agentic processing
- 🔍 **Agentic RAG Pipeline**: Hybrid retrieval combining BM25 + vector search with document reranking
- 🧠 **Persistent Conversational Memory**: Long-term memory backed by Qdrant, PostgreSQL, and Neo4j
- 🗺️ **Knowledge Graph Reasoning**: Relationship-aware reasoning across sessions via a Neo4j knowledge graph
- 📄 **Semantic Chunking & Query Planning**: Context-aware document chunking with intelligent query decomposition
- 🚀 **Async Architecture**: Full async/await support for high-throughput, low-latency performance

## Quick Start

### Prerequisites
- Python 3.12+
- Qdrant server running
- Neo4j server running
- PostgreSQL database

### Installation
```bash
git clone https://github.com/Atrocian10/Conversational_Knowledge_AI

# Install Python dependencies
pip install -r requirements.txt

# ⚠️ IMPORTANT: Set up external services before running
# The system requires several external services to function:
# See SETUP.md for detailed instructions

# Quick setup summary:
# 1. Start Qdrant (vector store)        - See SETUP.md
# 2. Start Neo4j (knowledge graph)      - See SETUP.md
# 3. Configure PostgreSQL (memory store) - See SETUP.md
```

### Basic Usage
```bash
# Start the voice assistant
python -m src run

# Run the demo with memory integration
python demo_research.py
```

### Programmatic Usage
```python
import asyncio
from src.orchestrator import KnowledgeNavigator
from src.agents.retrieval_agent import RetrievalAgent
from src.agents.planning_agent import PlanningAgent

async def main():
    navigator = KnowledgeNavigator(use_memory=True)
    await navigator.initialize()

    navigator.add_agent("retrieval", RetrievalAgent())
    navigator.add_agent("planner", PlanningAgent())
    navigator.build_graph()

    result = await navigator.process_request(
        "What were the key decisions from last week's sessions?",
        thread_id="voice-session-001"
    )

    print(result["messages"][-1].content)
    await navigator.cleanup()

asyncio.run(main())
```

## Architecture

### Core Components
- **Orchestrator**: Stateful agentic workflow built on LangGraph `StateGraph`
- **Planning Agent**: Query planning, task decomposition, and retrieval strategy selection
- **Retrieval Agent**: Hybrid BM25 + vector search with semantic reranking
- **Memory Bridge**: Persistent memory layer integrating Qdrant, PostgreSQL, and Neo4j
- **Knowledge Graph**: Neo4j-backed relationship store for cross-session reasoning

### Technology Stack
- **Orchestration**: LangGraph — stateful multi-step agentic workflows
- **API Layer**: FastAPI — async REST interface for agent interactions
- **Vector Store**: Qdrant — dense vector search and memory persistence
- **Graph Database**: Neo4j — knowledge graph for relationship-aware reasoning
- **Relational Store**: PostgreSQL — structured long-term memory storage
- **Language**: Python 3.12 with full async/await

### RAG Pipeline
1. **Query Planning** — decompose and expand user queries before retrieval
2. **Hybrid Retrieval** — BM25 sparse search + dense vector search over Qdrant
3. **Reranking** — cross-encoder reranking for precision-focused results
4. **Semantic Chunking** — context-preserving document segmentation
5. **Knowledge Graph Lookup** — entity and relationship enrichment from Neo4j

## Configuration

### Environment Variables
```bash
# Qdrant Configuration
export QDRANT_URL="http://localhost:6333"
export QDRANT_API_KEY="your_api_key"  # optional

# Neo4j Configuration
export NEO4J_URL="bolt://localhost:7687"
export NEO4J_USER="neo4j"
export NEO4J_PASSWORD="your_password"

# PostgreSQL Configuration
export POSTGRES_URL="postgresql://localhost:5432/knowledge_nav"

# LLM Configuration
export MODEL_PATH="/path/to/your/model.gguf"
export GPU_LAYERS=-1

# App Settings
export MAX_INPUT_LENGTH=10000
export LOG_LEVEL=INFO
```

### Required Services
1. **Qdrant**: Vector database for retrieval and memory (`http://localhost:6333`)
2. **Neo4j**: Knowledge graph for relationship reasoning (`bolt://localhost:7687`)
3. **PostgreSQL**: Long-term structured memory store (`localhost:5432`)

## Project Structure
```
agents/
├── src/                        # Core system
│   ├── orchestrator.py        # LangGraph stateful workflow
│   ├── agents/                # Agent implementations
│   │   ├── retrieval_agent.py # Hybrid RAG retrieval
│   │   ├── planning_agent.py  # Query planning & decomposition
│   │   └── execution_agent.py # Task execution
│   ├── memory_bridge.py       # Qdrant + Neo4j + PostgreSQL integration
│   └── config.py              # Configuration management
├── demo_research.py           # End-to-end workflow demo
└── docs/                      # Documentation
```

## Testing
```bash
# Test retrieval pipeline
python -m src run "What is the capital of France?"

# Test full demo with memory
python demo_research.py

# Test memory bridge independently
python src/memory_bridge.py
```

## Development

### Adding New Agents
```python
from src.orchestrator import BaseAgent

class MyAgent(BaseAgent):
    def __init__(self):
        super().__init__("my_agent")

    async def process(self, state):
        # Agent logic here
        return {
            "messages": [AIMessage(content="Done")],
            "current_agent": "orchestrator"
        }

# Register with the navigator
navigator.add_agent("my_agent", MyAgent())
```

### Error Handling
The system enforces strict error handling principles:
- No silent failures — all errors are logged with full context
- Comprehensive troubleshooting information on failure
- Proper resource cleanup in all execution paths

## Dependencies

### Core Requirements
- `langgraph` — Stateful multi-agent orchestration
- `langchain>=0.3.25` — LLM framework and tooling
- `fastapi` — Async REST API layer
- `aiohttp` — Async HTTP client

### Memory & Retrieval
- `qdrant-client>=1.7.0` — Vector store client
- `neo4j>=5.0.0` — Graph database driver
- `psycopg2-binary` — PostgreSQL adapter
- `rank-bm25` — BM25 sparse retrieval
- `sentence-transformers` — Dense embeddings and reranking

### Optional
- `llama-cpp-python` — Local LLM inference
- `pyaudio` — Voice input streaming

## License

MIT License — See LICENSE file for details.

## Status

**Version**: 1.0.0  
**Status**: Production Ready  
**Test Coverage**: All core pipelines tested and validated  

A production-grade voice assistant that combines real-time speech processing, agentic RAG, and persistent memory to deliver context-aware, relationship-informed knowledge retrieval across sessions.
