# Technical Architecture

## 1. System Overview
-   **Type**: Configuration Repository / Knowledge Base
-   **Core Technologies**: Markdown, PowerShell (for sync scripts)

## 2. Component Diagram
-   **Rules (`rules/`)**: RESPONSIBILITY: Stores global behavioral rules (`GEMINI.md`). INTERACTS WITH: Agent decision making.
-   **Skills (`skills/`)**: RESPONSIBILITY: Stores executable agent capabilities. INTERACTS WITH: Agent tool usage.
-   **Workflows (`workflows/`)**: RESPONSIBILITY: Stores standard operating procedures. INTERACTS WITH: Agent planning.

## 3. Data Flow
-   **Backup**: local `.gemini` -> `ContextManagement` repo.
-   **Restore**: `ContextManagement` repo -> local `.gemini`.

## 4. Key Decisions & Trade-offs
-   **Decision**: Use a dedicated repository for context.
-   **Rationale**: decoupling configuration from specific project code allows for portability and versioning of the "brain" itself.
