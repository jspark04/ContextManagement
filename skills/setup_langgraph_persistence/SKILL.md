---
name: setup_langgraph_persistence
description: Sets up LangGraph persistence using AsyncPostgresSaver with correct connection pooling and autocommit settings to avoid transaction errors.
---

# Setup LangGraph Persistence

Use this skill when you need to enable state persistence for LangGraph agents using a PostgreSQL database.

## 1. Dependencies

Ensure `psycopg-pool` is installed for async connection pooling.

```bash
pip install langgraph psycopg psycopg-pool
```

## 2. Create `checkpoint.py`

Create a module `app/checkpoint.py` (or `agents/checkpointer.py`) to manage the connection pool.

**CRITICAL**: You must set `autocommit=True` when initializing the connection for `checkpointer.setup()`, otherwise `CREATE INDEX CONCURRENTLY` will fail with `ActiveSqlTransaction`.

```python
from contextlib import asynccontextmanager
from psycopg_pool import AsyncConnectionPool
from langgraph.checkpoint.postgres.aio import AsyncPostgresSaver
from app.config import get_settings

_pool: AsyncConnectionPool = None

async def setup_checkpointer():
    """Initialize the connection pool and create checkpoint tables."""
    global _pool
    settings = get_settings()
    # Use sync connection string format for psycopg (e.g. postgresql://user:pass@host:port/db)
    # If using asyncpg connection string, adapter might be needed, but psycopg usually handles standard postgresql://
    conn_str = settings.database_url.replace("postgresql+asyncpg://", "postgresql://")
    
    _pool = AsyncConnectionPool(conn_str, max_size=20)
    
    # Run setup (creates tables)
    # MUST use autocommit=True for the setup phase to allow CREATE INDEX CONCURRENTLY
    async with _pool.connection() as conn:
        await conn.set_autocommit(True)
        checkpointer = AsyncPostgresSaver(conn)
        await checkpointer.setup()

async def shutdown_checkpointer():
    """Close the connection pool."""
    global _pool
    if _pool:
        await _pool.close()

def get_checkpointer_pool():
    """Returns the global connection pool."""
    return _pool
```

## 3. Configure Lifespan in FastAPI

In `app/main.py`:

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI
from app.checkpoint import setup_checkpointer, shutdown_checkpointer

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    await setup_checkpointer()
    yield
    # Shutdown
    await shutdown_checkpointer()

app = FastAPI(lifespan=lifespan)
```

## 4. Usage in Graph Execution

In your route (e.g., `app/routers/chat.py`):

```python
from langgraph.checkpoint.postgres.aio import AsyncPostgresSaver
from app.checkpoint import get_checkpointer_pool
from app.agents.graph import build_graph

# ... inside endpoint ...
    pool = get_checkpointer_pool()
    
    # Use a connection from the pool
    async with pool.connection() as conn:
        checkpointer = AsyncPostgresSaver(conn)
        
        # Compile graph with checkpointer
        # Ensure your build_graph function accepts checkpointer and calls graph.compile(checkpointer=checkpointer)
        graph = build_graph(checkpointer=checkpointer)
        
        # Invoke with configurable thread_id
        config = {"configurable": {"thread_id": str(trip_id)}}
        result = await graph.ainvoke(state, config=config)
```
