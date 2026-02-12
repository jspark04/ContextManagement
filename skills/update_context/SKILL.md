---
name: update_context
description: Guidelines and instructions for safely updating the project's 'context/' files (product.md, architecture.md, tasks.md). Use this when tracking progress, updating specs, or documenting architectural changes.
---

# Instructions

## 1. General Rule: Read Before Write
-   **Step 1**: ALWAYS read the target file first (`view_file context/product.md`, etc.).
-   **Reason**: To preserve existing context and avoid overwriting critical information.

## 2. Updating `product.md`
-   **When**: Scope changes, new feature requested, or vision refinement.
-   **How**:
    -   Keep it high-level (Executive Summary style).
    -   Update "Core Features" to reflect what is *actually* being built.
    -   Do NOT add implementation details (that goes in `architecture.md`).

## 3. Updating `architecture.md`
-   **When**: Design changeds, new database, new framework, or component refactor.
-   **How**:
    -   Update "System Overview" if the tech stack changes.
    -   Update "Component Diagram" and "Data Flow" to reflect reality.
    -   Log "Key Decisions" when a major trade-off is made (e.g., "Switched to PostgreSQL for JSONB support").

## 4. Updating `tasks.md` (Concurrency Protocol)
-   **Critical**: For all task management actions (locking groups, picking tasks, completing items), **YOU MUST USE THE `manage_project_tasks` SKILL**.
-   **Reason**: That skill contains the strict safety protocols (lock verification, semaphore logic) to prevent agent collisions.
-   **This Skill**: Use this skill only for structural updates to `tasks.md` (e.g. re-organizing sections) that don't involve active work management.

# Constraints
-   **Path**: Always target `context/` configuration files in the root.
-   **Locking**: NEVER ignore an active lock in `tasks.md`.
-   **Format**: Maintain the Markdown structure (Headers, lists).
