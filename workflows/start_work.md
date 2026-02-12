---
description: Analyzes the project's task list, picks the next best task group, and asks the user for permission to start.
---

1.  **Check Context Existence**:
    -   Check if `context/tasks.md` exists.
    -   **If NO**:
        -   Inform the user: "No task context found (`context/tasks.md`)."
        -   Ask: "Would you like to run the `initialize_context` workflow first?"
        -   **STOP**.

2.  **Analyze Task Context**:
    -   Read `context/tasks.md`.
    -   **Check User Input**: Did the user specify a topic or group (e.g., "work on auth")?
        -   **If YES**: Look for a matching Task Group. Check if it's unlocked.
            -   *If Locked*: Warn user ("Group X is locked by Agent Y"). Ask to proceed anyway (force) or pick another.
            -   *If Unlocked*: Select this group.
        -   **If NO**: Follow standard prioritization (below).
    -   **Standard Prioritization**:
        -   **Check Active Locks**: Look at `**Current Active Groups**`.
        -   **Identify Candidates**: Unlocked groups with incomplete tasks.
        -   **Prioritize**: Critical path or first sequential group.

3.  **Propose Next Step**:
    -   **Selected Group**: [Name of the group you picked]
    -   **Reason**: [Why? e.g. "It is the foundation for the next feature."]
    -   **Notify User**:
        -   State: "I propose starting on **[Selected Group]**."
        -   List 1-2 key tasks in that group.
        -   Ask: "Shall I proceed with this group? (I will lock it and begin work)."

4.  **On Confirmation (Next Turn)**:
    -   If the user says "Yes":
        -   Use the `update_context` skill to **LOCK** the group in `tasks.md`.
        -   Begin executing the tasks.
