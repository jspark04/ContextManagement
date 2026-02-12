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
-   **When**: Starting work, finishing work, or planning new work.
-   **Protocol**:
    1.  **Check Locks**: Read "Current Active Groups". Is another agent working on your target group?
        -   *If YES*: **STOP**. Do not touch files in that group. Work on something else or wait.
        -   *If NO*: Proceed.
    2.  **Locking (Starting Work)**:
        -   Mark the group in "Current Active Groups":
            -   `[ ] Group A` -> `[x] Group A (Agent: [MyName/Role])`
    3.  **Unlocking (Finishing/Pausing)**:
        -   Unmark the group:
            -   `[x] Group A ...` -> `[ ] Group A`
    4.  **Adding Tasks**:
        -   **New Group**: If tasks involve a *distinct* set of files, create a NEW group.
        -   **Existing Group**: If tasks involve files *already* in a group, add them there to ensure serial execution.

# Constraints
-   **Path**: Always target `context/` configuration files in the root.
-   **Locking**: NEVER ignore an active lock in `tasks.md`.
-   **Format**: Maintain the Markdown structure (Headers, lists).
