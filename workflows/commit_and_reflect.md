---
description: Updates documentation, context, commits changes, and runs a retrospective.
---

1.  **Update Documentation**:
    -   Check if `README.md` exists in the root.
    -   If it exists, analyze recent changes and update the "Recent Changes" or relevant sections.

2.  **Update Context**:
    -   **Execute Skill**: Read and follow the instructions in `../skills/update_context/SKILL.md`.
    -   **Goal**: Ensure `product.md`, `architecture.md`, and `tasks.md` are up to date and any active task locks are released.

3.  **Commit Changes**:
    -   Stage all changes: `git add .`
    -   Generate a descriptive commit message based on the changes.
    -   Commit: `git commit -m "your message"`
    -   **Push**: Check if a remote is configured (`git remote -v`). If yes, push to the current branch (`git push`).

4.  **Run Retrospective**:
    -   Execute the `/retrospective` workflow to analyze the session and update global rules.

5.  **Notify User**:
    -   Inform the user that documentation is updated, changes are committed, and the retrospective is complete.
