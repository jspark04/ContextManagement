---
name: create_new_workflow
description: Creates a new workflow file in the global workflows directory. Use this when the user asks to "create a workflow" or "automate a process".
---

# Instructions
1.  **Understand the Goal**: Identify the purpose and steps of the workflow the user wants to create.
2.  **Define Paths**:
    -   Workflow Name: `[workflow_name]` (snake_case, e.g., `Update_Readme`). Note: Workflow files use `snake_case` or `kebab-case`.
    -   Target Directory: `C:\Users\johnp\.gemini\antigravity\global_workflows`
    -   Target File: `C:\Users\johnp\.gemini\antigravity\global_workflows\[workflow_name].md`
3.  **Create Directory**: Ensure the target directory exists.
4.  **Create Workflow File**: Write the `[workflow_name].md` file with the following template:

    ```markdown
    ---
    description: [Brief description of what the workflow does. e.g. "updates the readme file"]
    ---
    [Step-by-step instructions on how to run this workflow.]

    1. [Step 1]
    2. [Step 2]
    // turbo
    3. [Step 3 (This run_command step will be auto-run)]
    ```

5.  **Notify User**: Confirm the workflow has been created and is ready to use.

# Constraints
-   **Structure**: ALWAYS use the `C:\Users\johnp\.gemini\antigravity\global_workflows` directory.
-   **Naming**: Workflow filenames MUST be `snake_case` or `kebab-case`.
-   **Descriptions**: The description in the frontmatter MUST NOT contain colons (`:`).
-   **Turbo Usage**: Use `// turbo` sparingly, only for safe, non-destructive commands. Use `// turbo-all` if the entire workflow is safe automation.
