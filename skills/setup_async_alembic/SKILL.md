---
name: setup_async_alembic
description: Sets up Alembic migrations alongside async SQLAlchemy, handling the sync/async driver split correctly
---

# Setup Alembic with Async SQLAlchemy

This skill sets up Alembic database migrations in a project that uses **async SQLAlchemy** (`asyncpg`). The key gotcha is that Alembic requires a **synchronous** database driver, so you need two DB URLs.

## Prerequisites
- `sqlalchemy[asyncio]`, `asyncpg` (async app driver)
- `psycopg[binary]` (sync Alembic driver)
- `alembic`

## Steps

### 1. Define Two DB URLs in `.env`
```env
# Async — used by the FastAPI app
DATABASE_URL=postgresql+asyncpg://user:pass@host:5432/dbname
# Sync — used ONLY by Alembic migrations
DATABASE_URL_SYNC=postgresql+psycopg://user:pass@host:5432/dbname
```

### 2. Expose Both in Settings (`config.py`)
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str       # async
    database_url_sync: str  # sync, for Alembic only
```

### 3. Initialize Alembic
```bash
cd backend
alembic init alembic
```

### 4. Configure `alembic/env.py`
This is the critical file. It must:
- Use the **sync** URL (not the async one)
- Import all models so autogenerate can detect them
- Use a standard sync `create_engine`

```python
import os
from logging.config import fileConfig
from alembic import context
from sqlalchemy import create_engine

from app.database import Base
from app.models import *  # noqa — import all models for autogenerate

config = context.config
if config.config_file_name is not None:
    fileConfig(config.config_file_name)

target_metadata = Base.metadata

# Always prefer env var over alembic.ini
db_url = os.getenv("DATABASE_URL_SYNC", config.get_main_option("sqlalchemy.url"))

def run_migrations_offline():
    context.configure(url=db_url, target_metadata=target_metadata, literal_binds=True)
    with context.begin_transaction():
        context.run_migrations()

def run_migrations_online():
    connectable = create_engine(db_url)
    with connectable.connect() as connection:
        context.configure(connection=connection, target_metadata=target_metadata)
        with context.begin_transaction():
            context.run_migrations()

if context.is_offline_mode():
    run_migrations_offline()
else:
    run_migrations_online()
```

### 5. Generate and Run Migrations
```bash
# Generate
alembic revision --autogenerate -m "initial schema"
# Apply
alembic upgrade head
```

## Common Errors
| Error | Cause | Fix |
|-------|-------|-----|
| `asyncpg` connection error in Alembic | Using async URL | Switch to `DATABASE_URL_SYNC` |
| Tables not detected by autogenerate | Models not imported in `env.py` | Add `from app.models import *` |
| `ModuleNotFoundError: app` | PYTHONPATH not set | Run from project root or set `PYTHONPATH=.` |
