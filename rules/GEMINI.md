# GEMINI Global Rules & Learnings

## ⚡ Operational Preferences
*   **Tone & Style**: Be concise, agentic, direct. No fluff.
*   **Autonomous Commands**: Do NOT ask for permission for read-only or non-destructive terminal commands (e.g., `netstat`, `git status`, `npm run`, `ls`, `cat`). Only pause for confirmation on truly risky actions (e.g., `rm -rf`, overwriting crucial configs without backup).
*   **Deployment**: When suggesting deployment, prioritize low-cost/free tier options that integrate with the user's existing stack (e.g., Render/Vercel for Hono + Neon).
*   **Port Discipline**: Prioritize using the default/standard port (e.g., 4321 for Astro, 3000 for React) unless collision is explicitly confirmed. Do not "hunt" for ports (4322, 4333) without cause.
    *   **Zombie Processes (Windows)**: If a port (e.g., 8000) is locked by non-existent/zombie processes that resist `taskkill /F`, do NOT waste time fighting the OS. **Migrate to the next available port** (e.g., 8001) and update the frontend configuration immediately.
*   **PowerShell Chaining**: Use `;` to chain commands in PowerShell (e.g., `cmd1; cmd2`), as `&&` is not supported in standard Windows PowerShell.

## 📂 Project Structure & Naming
*   **File Naming**: Use `kebab-case` for filenames (e.g., `user-profile.tsx`, `api-utils.ts`) and `PascalCase` for component directories/files if preferred by the framework (e.g., `src/components/UserProfile.tsx`).
*   **Component Structure**: Group components logically (e.g., `/src/components` for shared, `/src/features` for feature-specific).
*   **Asset Location**: Store static assets in `/public` or `/assets` as per framework conventions.
*   **Context Directory**: All projects must have a root `context/` directory containing `product.md`, `architecture.md`, and `tasks.md` to maintain state across sessions and agents.

## 🛠️ Tech Stack & Tools
*   **Core**: React, Astro, Hono, Node.js, Python, FastAPI.
*   **AI/Agents**: LangGraph (Supervisor-Worker pattern), LangChain, OpenAI.
*   **Database**: Neon (Postgres).
*   **Styling**: TailwindCSS (preferred for speed) or Vanilla CSS (if requested for specific aesthetics).
*   **Package Manager**: `npm` (unless `pnpm` or `yarn` is present in the repo).
*   **Windows Process Management**: Use `taskkill /F /PID <pid>` to aggressively kill locked processes (like zombie Node servers) that refuse to die with Ctrl+C.

## 🛡️ Security Protocols
*   **Secrets Management**:
    *   **NEVER** commit `.env` files.
    *   **ALWAYS** create a `.gitignore` file immediately upon project initialization (especially for Node/Python/Go).
    *   **Verify** untracked files before running `git add .` to ensure no secrets are slipping in.
*   **Leak Remediation**: If a secret is leaked, immediate rotation is the only valid fix. History rewriting is secondary.
    *   Use `git-filter-repo` (via `pip install git-filter-repo`) to purge files from history: `python -m git_filter_repo --path <file> --invert-paths --force`.
    *   Remember to re-add the remote after `git-filter-repo` removes it, then force push.

## 💻 Coding Standards
*   **React Hooks**: ALWAYS declare hooks (`useState`, `useEffect`) at the very top of the functional component. NEVER place them after conditional returns (like `if (loading) return ...`), as this causes invariant violations.
*   **UX Polish**:
    *   Provide real-time feedback for waiting states (e.g., "Waiting for host...").
    *   Use affirmative confirmation (e.g., "My Choice" highlights) even when interactions are disabled.
    *   Catch logic errors (like `rooms.length !== users.length`) with friendly, descriptive user alerts instead of silent failures.
*   **Astro Framework**:
    *   **Scripts**: Avoid mixing `define:vars` with `import` statements in `<script>` tags, as this breaks bundling. Instead, pass data via `data-*` attributes and select elements within the script module.
    *   **Env Vars in Docker**: `import.meta.env` is inlined at build time by Vite. For secrets needed at runtime (e.g., `DATABASE_URL`, `ADMIN_PASSWORD`), ALWAYS use `process.env` so Docker containers can inject them. `import.meta.env.PROD` (Astro built-in) is fine.
*   **Third-Party Libraries**:
    *   **Quill.js**: The default `ql-snow` theme is difficult to style in dark mode. Use `!important` liberally for overriding `.ql-picker-options` and `.ql-tooltip` to prevent "white box" issues.
*   **Python/FastAPI**:
    *   Use `async` SQLAlchemy engine + session for non-blocking DB access.
    *   Use `pydantic-settings` (`BaseSettings`) for env-var config, not raw `os.getenv`.
    *   Use Pydantic V2 schemas for request/response models.
    *   Use lifespan context manager (not `@app.on_event`) for startup/shutdown hooks.
    *   **Alembic + Async**: Alembic migrations require a **sync** DB URL (e.g., `postgresql+psycopg://`), even when the app uses async (`asyncpg://`). Always define both `DATABASE_URL` (async) and `DATABASE_URL_SYNC` (sync for Alembic) in `.env`.
    *   **`dict.get()` gotcha**: `d.get("key", "")` returns `None` when the key exists with value `None`. Use `(d.get("key") or "")` for null-safe defaults.
    *   **Windows Terminal Encoding**: Avoid printing emojis directly in scripts intended for Windows terminals, as this causes `UnicodeEncodeError`. Use ASCII alternatives or ensure `PYTHONIOENCODING=utf-8`.
    *   **Version Compatibility**: Avoid Python 3.14+ for now due to `asyncpg` build failures and `SQLAlchemy` typing issues. Prefer Python 3.12 or run backend via Docker if native version is too new.
    *   **Blocking I/O**: Wrap blocking synchronous calls (e.g., third-party SDKs like `TavilyClient`, `requests`) in `await asyncio.to_thread(...)` to prevent freezing the event loop.
    *   **Console Output**: NEVER use raw emojis in backend logs/scripts intended for Windows consoles as this causes `UnicodeEncodeError`. Use `[INFO]`/`[WARN]` tags or ensure `PYTHONIOENCODING=utf-8`.
    *   **Database Resilience**: For hybrid Local/Docker setups where hostnames like `db` may be unresolvable, implement a TCP pre-check before initializing heavy connection pools (`AsyncConnectionPool`). Only create the pool if the host is reachable; otherwise, fallback to `localhost` or `MemorySaver`.
    *   **Python 3.14 Compatibility**: Reinforce strict dependency pinning (e.g., `SQLAlchemy>=2.0.46`) if running on Python 3.14+, or prefer Python 3.12 until ecosystem catches up.

### AI/LLM Patterns
*   **Tiered Models**: For cost-efficiency, implement a "Fast" vs "Smart" model tier system (e.g., `get_llm(tier="fast")`). Default both to a cheap/capable model (like Gemini Flash) but allow easy scaling to Pro/GPT-4 for complex tasks.
*   **Resilient Fallback**: All AI-dependent features (like `chat/stream`) MUST have a deterministic fallback (e.g., standard POST endpoint) that returns the same structured data (itinerary, profile) if the primary AI transport fails. Never leave the UI in an empty state due to a transport error.

## 🧪 Testing & Quality
*   **Frameworks**: Use Vitest (preferred) or Jest for unit testing.
*   **Critical Paths**: Focus tests on critical user flows and complex logic.
*   **Linting**: Respect existing linter rules; propose fixes for lint errors immediately.

## 📝 Documentation
*   **Keep Updated**: Update `README.md` and other documentation when adding new features or changing architectures.
*   **Clarity**: Ensure documentation is clear, concise, and easy to follow.
*   **Env Var Accuracy**: When documenting `.env` variables (e.g., in README or Walkthroughs), ensure the example structure **EXACTLY** matches the actual `.env` file structure (comments, ordering). Mismatches cause user confusion.

## ⚙️ Workflows & Automation
*   **Antigravity Workflows**:
    *   **Description Format**: Workflow descriptions in `.agent/workflows/*.md` must NOT contain colons (:). This breaks visibility in the IDE.
    *   **Visibility**: Commit workflow files to git immediately so they appear in the IDE.
    *   **Chaining**: Structure workflows to chain operations (e.g., Update README -> Commit -> Push) for complex tasks.
    *   **Workflow Design**:
        *   **Turbo Mode**: Use `// turbo-all` for workflows that are safe and fully automated, especially maintenance tasks.
        *   **Visibility**: Scripts should print a concise summary of actions (e.g., "Created: X, Updated: Y") rather than failing silently or succeeding without output.
*   **Docker**:
    *   **Tagging**: Always tag images with both a unique timestamp/version AND `latest`.
    *   **Automation**: Create automation scripts (e.g., `push_image.ps1`) to handle the build/tag/push process.
    *   **PowerShell**: Use `-ExecutionPolicy Bypass` when running `.ps1` scripts. If blocked by system policy, fallback to `cmd /c "command"` for simple executions.
    *   **Safe Chaining**: NEVER use `;` to chain dependent commands (like `build; deploy`) because it ignores failures. NEVER use `&&` directly in PowerShell. **ALWAYS** wrap chained commands in `cmd /c "cmd1 && cmd2"` to ensure safety and compatibility.
    *   **Windows CMD**: When using `set` in `cmd`, ALWAYS quote the assignment (e.g., `set "VAR=value"`) to prevent accidental trailing whitespace from being included in the value.
    *   **Postgres**: Always pin to a major version (e.g., `postgres:16-alpine`), never use bare `postgres:alpine` — major version upgrades (e.g., PG 18) break data directory conventions.
    *   **Next.js**: Set `output: "standalone"` in `next.config.ts` when building multi-stage Docker images. The runner stage copies from `.next/standalone` and `.next/static`.
    *   **URL Hygiene**: Always `.trim()` environment variables that contain URLs (e.g., `API_URL`) to prevent hard-to-debug "Failed to parse URL" errors from trailing whitespace.
    *   **Local Env Vars**: For Next.js local development, ALWAYS use `.env.local` for overrides (like changing ports). Root `.env` changes are often ignored by `next dev` if not explicitly loaded.
    *   **npx**: Always use `npx -y` (auto-yes) and pass all flags to avoid interactive prompts that stall background commands.
    *   **Pipe Errors**: If `npipe:////./pipe/dockerDesktopLinuxEngine` errors occur on Windows, prompt the user to **restart Docker Desktop** or the entire system immediately. This is a common engine failure mode. Do NOT attempt code workarounds (like switching to SQLite) for infrastructure failures.
    *   **Env Var Passthrough**: `docker-compose` does NOT automatically pass host env vars to containers. You must explicitly map them in the `environment` section (e.g., `API_KEY: ${API_KEY}`).

## 📚 Frameworks & Libraries
*   **Firebase Genkit**:
    *   **Versioning**: Pin `zod` and `zod-to-json-schema` in `package.json` overrides to ensure compatibility (Genkit 1.28.0+ often requires standard schema support).
    *   **Configuration**: Use `genkit()` from the `genkit` package for initialization, not `configureGenkit` from `@genkit-ai/core`.
    *   **Flows & Tools**: Use `ai.defineFlow` and `ai.defineTool` on the initialized instance.
    *   **Generation**: `ai.generate` returns an object where `.output` is a getter (nullable), not a function.
### LangGraph & AI
*   **PostgresSaver Persistence**: When using `AsyncPostgresSaver`, ALWAYS initialize the connection with `autocommit=True` to prevent `psycopg.errors.ActiveSqlTransaction` errors during checkpoint setup. Warning: `AsyncPostgresSaver` does NOT currently support the async context manager protocol (`async with`) directly — instantiate it normally and await `.setup()`.
*   **AI Resilience**: Implement deterministic fallback logic for AI components. If an LLM call fails (e.g., quota exceeded, timeout), the agent MUST have a code-based path to continue the workflow.
*   **LLM Provider Abstraction**: Use a factory pattern (e.g., `get_llm(tier="fast")`) to decouple application logic from specific providers (OpenAI/Gemini). This enables cost optimization and failover.
*   **JSON Parsing**: ALWAYS strip markdown code blocks (```json ... ```) before parsing JSON responses from LLMs, even if the prompt forbids markdown. Models often ignore negative constraints.
*   **State Schema**: `TypedDict` state schemas OVERWRITE keys on update. To preserve state (like search results) across turns, omit those keys from the input dictionary in subsequent invocations.
*   **Verification Scripts**:
    *   Set `PYTHONPATH` to include the project root (e.g., `$env:PYTHONPATH="backend;..."`).
    *   **Windows Loop Policy**: For strict async compatibility (especially with `asyncpg`/Postgres), explicitly set `asyncio.set_event_loop_policy(asyncio.WindowsSelectorEventLoopPolicy())` on Windows. The default Proactor loop fails with `asyncpg`.
    *   **Output hygiene**: Avoid printing raw emojis in scripts intended for Windows terminals (`UnicodeEncodeError`). Use `sys.stdout.reconfigure(encoding='utf-8')` or strip non-ASCII characters.

### Frontend Patterns
*   **SSE Parsing**: When parsing Server-Sent Events (SSE) in React/JS, ALWAYS handle data chunks that may be split across multiple packets. Accumulate the `data` buffer and append new content (e.g., `currentData += line.slice(5)`) rather than overwriting it, as large JSON generation is often chunked.
*   **React Forms**: For complex inputs like `type="date"`, prefer uncontrolled components (remove `value`, use `defaultValue`) or robust `onBlur` handlers to prevent "typing clearing" issues caused by strict React re-renders on partial inputs.
*   **Date Parsing**: Avoid `new Date("YYYY-MM-DD")` in JS/TS as it parses as UTC, often leading to off-by-one errors in local time. Split and map to numbers (`str.split("-").map(Number)`) or use a library.
*   **UI Testing / Wizard Bypass**: For complex flows (like onboarding wizards), implement a developer bypass (e.g., `setupComplete={true}` prop or mock data injection) to facilitate isolated component testing and browser automation.

