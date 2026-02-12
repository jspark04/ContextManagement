---
description: Acts as a product thought partner to propose 3 high-value features/enhancements based on project context, warning about infra heavy/costly options.
---

1.  **Analyze Context**:
    -   Read `context/product.md`, `context/architecture.md`, `context/tasks.md`.
    -   Briefly scan the codebase to understand the current maturity and tech stack.

2.  **Brainstorm (Product Thought Partner)**:
    -   Generate 3 distinct feature or enhancement ideas.
    -   **Prioritization Criteria**:
        -   **High Value**: Solves a user problem or significant debt.
        -   **Low Overhead**: Prefer solutions that fit the existing stack (e.g., SQLite over new DB, static site over new backend).
    -   **Critical Warning**: If an idea requires new infrastructure (managed DB, new server, paid service), you MUST flag it: "⚠️ Requires Extra Infra/Cost".

3.  **Present Options**:
    -   Present the 3 ideas concisely:
        1.  **[Name]**: [What it is] - [Why it's valuable]. (⚠️ Warnings if any).
        2.  ...
        3.  ...
    -   **Action**: Ask the user: "Which of these (if any) should we add to the roadmap?"

4.  **Process Selection (Next Turn)**:
    -   **If User Selects**:
        -   Open `context/tasks.md`.
        -   Use the `update_context` skill (see `C:\Users\johnp\.gemini\antigravity\skills\update_context\SKILL.md`) to add the new work.
        -   **Grouping**: Create a **NEW Task Group** for the feature to ensure clean separation.
        -   **Update Product**: If it's a major feature, update `context/product.md` as well.
