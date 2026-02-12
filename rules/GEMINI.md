# GEMINI Global Rules & Learnings

## ⚡ Operational Preferences
*   **Tone & Style**: Be concise, agentic, direct. No fluff.
*   **Autonomous Commands**: Do NOT ask for permission for read-only or non-destructive terminal commands (e.g., `netstat`, `git status`, `npm run`, `ls`, `cat`). Only pause for confirmation on truly risky actions (e.g., `rm -rf`, overwriting crucial configs without backup).
*   **Deployment**: When suggesting deployment, prioritize low-cost/free tier options that integrate with the user's existing stack (e.g., Render/Vercel for Hono + Neon).
*   **Port Discipline**: Prioritize using the default/standard port (e.g., 4321 for Astro, 3000 for React) unless collision is explicitly confirmed. Do not "hunt" for ports (4322, 4333) without cause.

## 📂 Project Structure & Naming
*   **File Naming**: Use `kebab-case` for filenames (e.g., `user-profile.tsx`, `api-utils.ts`) and `PascalCase` for component directories/files if preferred by the framework (e.g., `src/components/UserProfile.tsx`).
*   **Component Structure**: Group components logically (e.g., `/src/components` for shared, `/src/features` for feature-specific).
*   **Asset Location**: Store static assets in `/public` or `/assets` as per framework conventions.
*   **Context Directory**: All projects must have a root `context/` directory containing `product.md`, `architecture.md`, and `tasks.md` to maintain state across sessions and agents.

## 🛠️ Tech Stack & Tools
*   **Core**: React, Astro, Hono, Node.js.
*   **Database**: Neon (Postgres).
*   **Styling**: TailwindCSS (preferred for speed) or Vanilla CSS (if requested for specific aesthetics).
*   **Package Manager**: `npm` (unless `pnpm` or `yarn` is present in the repo).

## 🛡️ Security Protocols
*   **Secrets Management**:
    *   **NEVER** commit `.env` files.
    *   **ALWAYS** create a `.gitignore` file immediately upon project initialization (especially for Node/Python/Go).
    *   **Verify** untracked files before running `git add .` to ensure no secrets are slipping in.
*   **Leak Remediation**: If a secret is leaked, immediate rotation is the only valid fix. History rewriting is secondary.

## 💻 Coding Standards
*   **React Hooks**: ALWAYS declare hooks (`useState`, `useEffect`) at the very top of the functional component. NEVER place them after conditional returns (like `if (loading) return ...`), as this causes invariant violations.
*   **UX Polish**:
    *   Provide real-time feedback for waiting states (e.g., "Waiting for host...").
    *   Use affirmative confirmation (e.g., "My Choice" highlights) even when interactions are disabled.
    *   Catch logic errors (like `rooms.length !== users.length`) with friendly, descriptive user alerts instead of silent failures.
*   **Astro Framework**:
    *   **Scripts**: Avoid mixing `define:vars` with `import` statements in `<script>` tags, as this breaks bundling. Instead, pass data via `data-*` attributes and select elements within the script module.
*   **Third-Party Libraries**:
    *   **Quill.js**: The default `ql-snow` theme is difficult to style in dark mode. Use `!important` liberally for overriding `.ql-picker-options` and `.ql-tooltip` to prevent "white box" issues.

## 🧪 Testing & Quality
*   **Frameworks**: Use Vitest (preferred) or Jest for unit testing.
*   **Critical Paths**: Focus tests on critical user flows and complex logic.
*   **Linting**: Respect existing linter rules; propose fixes for lint errors immediately.

## 📝 Documentation
*   **Keep Updated**: Update `README.md` and other documentation when adding new features or changing architectures.
*   **Clarity**: Ensure documentation is clear, concise, and easy to follow.

## ⚙️ Workflows & Automation
*   **Antigravity Workflows**:
    *   **Description Format**: Workflow descriptions in `.agent/workflows/*.md` must NOT contain colons (:). This breaks visibility in the IDE.
    *   **Visibility**: Commit workflow files to git immediately so they appear in the IDE.
    *   **Chaining**: Structure workflows to chain operations (e.g., Update README -> Commit -> Push) for complex tasks.
*   **Docker**:
    *   **Tagging**: Always tag images with both a unique timestamp/version AND `latest`.
    *   **Automation**: Create automation scripts (e.g., `push_image.ps1`) to handle the build/tag/push process.
    *   **PowerShell**: Use `-ExecutionPolicy Bypass` when running `.ps1` scripts from the agent to avoid permission errors.

## 📚 Frameworks & Libraries
*   **Firebase Genkit**:
    *   **Versioning**: Pin `zod` and `zod-to-json-schema` in `package.json` overrides to ensure compatibility (Genkit 1.28.0+ often requires standard schema support).
    *   **Configuration**: Use `genkit()` from the `genkit` package for initialization, not `configureGenkit` from `@genkit-ai/core`.
    *   **Flows & Tools**: Use `ai.defineFlow` and `ai.defineTool` on the initialized instance.
    *   **Generation**: `ai.generate` returns an object where `.output` is a getter (nullable), not a function.