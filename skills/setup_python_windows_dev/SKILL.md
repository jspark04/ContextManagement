---
name: setup_python_windows_dev
description: Sets up a robust local Python development environment on Windows, handling common pitfalls like asyncpg event loops, zombie processes, and buffered output.
---

# Setup Python Windows Dev

This skill provides a standard pattern for running Python/FastAPI servers on Windows to ensure maximum compatibility and developer experience.

## The Problem
Running Python servers (Uvicorn/FastAPI) on Windows often encounters specific issues:
1.  **Event Loop Incompatibility**: The default `ProactorEventLoop` crashes with `asyncpg` (Postgres driver).
2.  **Output Buffering**: Stdout often hangs or buffers, hiding debug prints.
3.  **Zombie Processes**: Killing the terminal doesn't always kill the process, locking ports (8000/8001).
4.  **Module Shadowing**: Local files named `app.py` or similar can shadow standard library or package modules.

## The Solution: `run_server.py`

Instead of running `uvicorn app.main:app` directly from the CLI, create a root-level `run_server.py` entrypoint.

### Template

Create `backend/run_server.py`:

```python
import sys
import os
import uvicorn

# 1. Force Unbuffered Output
# Critical for seeing logs in real-time on Windows terminals
sys.stdout.reconfigure(line_buffering=True)

# 2. Add CWD to Path
# Ensures "app" package is found correctly, avoiding site-packages shadowing
sys.path.insert(0, os.getcwd())

if __name__ == "__main__":
    # 3. Set Windows Event Loop Policy
    # REQUIRED for asyncpg/Postgres support on Windows
    if sys.platform == 'win32':
        import asyncio
        asyncio.set_event_loop_policy(asyncio.WindowsSelectorEventLoopPolicy())

    # 4. Define Port & Reload
    # Use a variable port if needed, or stick to a standard one (e.g. 8000)
    # Reload=True is great for dev, but might conflict with the custom runner logic in some edge cases. 
    # Usually better to run without reload for deep debugging, or use uvicorn CLI for simple cases.
    print("Starting uvicorn on 8000...")
    uvicorn.run("app.main:app", host="127.0.0.1", port=8000, log_level="info", reload=True)
```

## Usage
Run with:
```powershell
python run_server.py
```

## Troubleshooting Ports
If port 8000 is locked:
1.  **Don't** just try to kill it if `CTRL+C` failed.
2.  **Migrate**: Change `port=8001` (or higher) in `run_server.py`.
3.  **Update Frontend**: Update `.env.local` in the frontend to match.

## Verification
Always verify connectivity with:
```powershell
Test-NetConnection -ComputerName localhost -Port <PORT>
```
