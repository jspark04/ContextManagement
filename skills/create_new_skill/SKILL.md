---
name: create_new_skill
description: Creates a new skill by generating the directory and SKILL.md file. Use this when the user asks to "create a skill" or "learn a new skill".
---

# Instructions
1.  **Understand the Goal**: Identify the name and purpose of the skill the user wants to create.
2.  **Define Paths**:
    -   Skill Name: `[skill_name]` (snake_case, e.g., `git_commit_conventions`)
    -   Target Directory: `C:\Users\johnp\.gemini\antigravity\skills\[skill_name]`
    -   Target File: `C:\Users\johnp\.gemini\antigravity\skills\[skill_name]\SKILL.md`
3.  **Create Directory**: Ensure the target directory exists.
4.  **Create SKILL.md**: Write the `SKILL.md` file with the following template:

    ```markdown
    ---
    name: [skill_name]
    description: [Brief description of what the skill does]
    ---

    # Instructions
    [Step-by-step instructions on how to perform the skill. Be specific, atomic, and clear.]

    # Constraints
    -   [Constraint 1, e.g., "Do not delete files without asking."]
    -   [Constraint 2]

    # Examples
    ## Example 1: [Scenario]
    [Description of how the agent acts in this scenario]
    ```

5.  **Notify User**: Confirm the skill has been created and is ready to use.

# Constraints
-   **Structure**: ALWAYS use the `C:\Users\johnp\.gemini\antigravity\skills` directory.
-   **Naming**: Skill names MUST be `snake_case`.
-   **Content**: The `SKILL.md` MUST have a valid YAML frontmatter with `name` and `description`.
