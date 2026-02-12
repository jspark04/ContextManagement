---
description: Sync global rules, skills, and workflows to this repository.
---

# Sync Global to Repo

This workflow copies the latest global configurations from your `.gemini` directory to this repository.

## Steps

### 1. Sync Rules
-   **Action**: Copy `GEMINI.md` from global config.
-   **Command**:
    ```powershell
    Copy-Item "C:\Users\johnp\.gemini\GEMINI.md" "d:\Repos\ContextManagement\rules\GEMINI.md" -Force
    ```

### 2. Sync Skills
-   **Action**: Copy all skills from global config.
-   **Command**:
    ```powershell
    Copy-Item "C:\Users\johnp\.gemini\antigravity\skills\*" "d:\Repos\ContextManagement\skills\" -Recurse -Force
    ```

### 3. Sync Workflows
-   **Action**: Copy all global workflows.
-   **Command**:
    ```powershell
    Copy-Item "C:\Users\johnp\.gemini\antigravity\global_workflows\*" "d:\Repos\ContextManagement\workflows\" -Recurse -Force
    ```

### 4. Verify Changes
-   **Action**: Check git status to see if any files were updated.
-   **Command**:
    ```powershell
    git status
    ```

### 5. Next Steps
-   If changes are detected, use `/commit_and_reflect` to save them to the repository.
