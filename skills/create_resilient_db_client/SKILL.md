---
description: Implement a robust database connection pattern with TCP pre-checking and memory fallback.
---

# Create Resilient DB Client

This skill provides a pattern for establishing database connections that are resilient to environmental differences (e.g., Docker vs. Localhost) and network failures. It is particularly useful for robust agentic applications that need to function even when the primary persistence layer is unavailable.

## Problem
Standard connection pools (like `psycopg_pool` or `sqlalchemy`) often hang, retry indefinitely, or crash the application startup if the configured database host (e.g., `db` from Docker Compose) is unresolvable or unreachable on the host machine (e.g., Windows/Mac).

## Solution
Implement a **TCP Pre-flight Check** before instantiating the connection pool.
1.  **Parse** the connection string to extract host/port.
2.  **Test** TCP connectivity using `asyncio.open_connection` with a short timeout.
3.  **Fallback** to an alternative host (e.g., `127.0.0.1`) if the primary fails.
4.  **Graceful Degredation**: If all databases fail, fallback to an in-memory adapter (`MemorySaver`) so the application can still start.

## Implementation Pattern

Use this template for your `checkpoint.py` or database module:

```python
from contextlib import asynccontextmanager
import asyncio
import re
from langgraph.checkpoint.postgres.aio import AsyncPostgresSaver
from langgraph.checkpoint.memory import MemorySaver
from psycopg_pool import AsyncConnectionPool

async def _check_tcp_connection(conn_string: str, timeout: float = 1.0) -> bool:
    """Check if host/port in conn_string is reachable via TCP."""
    host = "localhost"
    port = 5432
    try:
        # Extract host/port (simplistic regex, adjust as needed)
        match = re.search(r"@([^:/]+)(?::(\d+))?\/", conn_string)
        if match:
            host = match.group(1)
            port = int(match.group(2)) if match.group(2) else 5432
        
        # Test connection
        future = asyncio.open_connection(host, port)
        reader, writer = await asyncio.wait_for(future, timeout=timeout)
        writer.close()
        await writer.wait_closed()
        return True
    except Exception:
        return False

@asynccontextmanager
async def lifespan_checkpointer(app):
    """Lifespan context manager with fallback logic."""
    settings = get_settings()
    primary_url = settings.database_url_sync.replace("+psycopg", "")
    
    # Define targets to try in order
    targets = [primary_url]
    if "@db:" in primary_url:
        targets.append(primary_url.replace("@db:", "@127.0.0.1:"))

    pool = None
    connected = False

    for i, cs in enumerate(targets):
        # 1. TCP Pre-check (Fail Fast)
        if not await _check_tcp_connection(cs):
            print(f"[WARN] Target {i+1} unreachable via TCP. Skipping.")
            continue
            
        # 2. Attempt Pool Creation
        try:
            # Create pool with NO background workers initially if possible
            # Psycopg 3 pool starts workers immediately, so we must be ready to close it
            current_pool = AsyncConnectionPool(
                conninfo=cs, 
                max_size=20, 
                open=False, 
                kwargs={"autocommit": True, "connect_timeout": 2}
            )
            
            # Open with strict timeout
            await asyncio.wait_for(current_pool.open(), timeout=2.0)
            
            # 3. Verify Query Capability
            async with current_pool.connection() as conn:
                checkpointer = AsyncPostgresSaver(conn)
                await checkpointer.setup()
            
            pool = current_pool
            connected = True
            print(f"[INFO] Connected to DB (Target {i+1}).")
            break
            
        except Exception as e:
            print(f"[WARN] Connection failed: {e}")
            if current_pool:
                await current_pool.close()

    # 4. Fallback to Memory
    if not connected:
        print("[WARN] No DB connection. Using In-Memory Checkpointer.")
        pool = None

    yield
    
    if pool:
        await pool.close()

@asynccontextmanager
async def get_checkpointer():
    """Context manager for dependency injection."""
    global _pool # logic to access the global pool created above
    if _pool:
        async with _pool.connection() as conn:
            yield AsyncPostgresSaver(conn)
    else:
        yield MemorySaver()
```
