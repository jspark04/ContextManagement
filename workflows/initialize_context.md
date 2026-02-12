---
description: Initializes the 'context/' directory with product spec, architecture doc, and parallelized task list, auto-filled based on project analysis.
---

1.  **Analyze Project Context**:
    -   **Explore**: List files in the root and secondary directories (e.g., `src`, `app`, `lib`) to understand the project structure.
    -   **Read Configs**: Read `package.json`, `go.mod`, `requirements.txt`, `README.md`, or other key configuration/documentation files.
    -   **Scan Code**: Briefly read main entry points (e.g., `index.ts`, `main.py`, `App.jsx`) and look for "TODO" comments.
    -   **Synthesize**: Formulate a mental model of the Product Name, Vision, Tech Stack, and Architecture based on this analysis.

2.  **Check/Create Context Directory**:
    -   Check if the `context/` directory exists in the project root.
    -   If not, create it.

3.  **Generate `context/product.md`**:
    -   Create `context/product.md` using the template below.
    -   **CRITICAL**: DO NOT leave placeholders like `[Name]`. Fill them in based on your analysis in Step 1.

    ```markdown
    # Product Specification

    ## 1. Overview
    -   **Product Name**: [Inferred Name]
    -   **Vision**: [Inferred Vision - what is this project doing?]
    -   **Target Audience**: [Inferred Audience]
    -   **Problem Solved**: [Inferred Problem]

    ## 2. Core Features (MVP)
    -   [Feature 1]: [Description based on existing code/docs]
    -   [Feature 2]: [Description]

    ## 3. User Flow
    -   [Inferred Flow: e.g., Login -> Dashboard -> Create Item]

    ## 4. Constraints & Non-Functional Requirements
    -   [Inferred Tech Constraints, e.g., "Must run on Node.js v14+"]
    ```

4.  **Generate `context/architecture.md`**:
    -   Create `context/architecture.md` using the template below.
    -   **CRITICAL**: Fill in the details based on your analysis of the codebase.

    ```markdown
    # Technical Architecture

    ## 1. System Overview
    -   **Type**: [e.g., React SPA, Express API, Python CLI]
    -   **Core Technologies**: [List actual libraries found, e.g., React, Tailwind, Redux]

    ## 2. Component Diagram
    -   **[Main Component found]**: RESPONSIBILITY: [What it seems to do]. INTERACTS WITH: [Other components].
    -   **[Database/Store]**: RESPONSIBILITY: [Data persistence].

    ## 3. Data Flow
    -   [Describe how data moves based on your code reading]

    ## 4. Key Decisions & Trade-offs
    -   **Decision**: [e.g., Choice of Framework]
    -   **Rationale**: [Inferred reason]
    ```

5.  **Generate `context/tasks.md`**:
    -   Create `context/tasks.md` using the template below.
    -   **Action**: particular attention to `TODO` comments found in code or obvious missing features (e.g. "auth implemented but no login page").

    ```markdown
    # Project Tasks

    ## Active Context
    <!-- Agents: update this signal when you start a group. -->
    **Current Active Groups**:
    -   [ ] Group A (Agent: [Name])
    -   [ ] Group B (Agent: [Name])

    ## Task Groups
    <!-- Groups are sets of tasks that can be done INDEPENDENTLY of other groups (minimized file overlap). -->

    ### Group A: [Inferred Topic 1]
    -   **Files Involved**: `[Related Files]`
    -   [ ] [Task derived from TODOs or missing features]
    -   [ ] [Another task]

    ### Group B: [Inferred Topic 2]
    -   **Files Involved**: `[Related Files]`
    -   [ ] [Task 3]
    ```

6.  **Notify User**:
    -   Inform the user that the context directory has been initialized and populated with inferred data.
    -   Ask them to review and correct any assumptions.
