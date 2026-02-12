---
name: manage_project_tasks
description: Best practices and actionable steps for using the Project Task Management System (context/tasks.md). Use this to List, Pick (Lock), Complete, or Add tasks while respecting concurrency.
---

# Instructions

## 1. Core Philosophy: Concurrency-Safe
We use `context/tasks.md` as a semaphore system to allow multiple agents to work on the same project without stepping on each other's toes (file collisions).

## 2. Actions

### A. LIST & ANALYZE
-   **Goal**: Find what to do next.
-   **Step**: Read `context/tasks.md`.
-   **Ignore**: Groups checked in `**Current Active Groups**`.
-   **Focus**: Groups that are UNLOCKED and have UNCHECKED tasks.

### B. PICK & LOCK (Start Work)
-   **Goal**: Claim a task group to work on.
-   **Step**:
    1.  Verify the group is NOT currently locked.
    2.  Add a line to `**Current Active Groups**`:
        `[x] Group Name (Agent: Me)`
-   **Why**: This signals to other agents "Hands off/Files busy".

### C. EXECUTE
-   **Goal**: Do the work.
-   **Step**: Read the tasks in your locked group and execute them using your other coding tools.

### D. COMPLETE & UNLOCK (Finish Work)
-   **Goal**: Release the group and mark progress.
-   **Step**:
    1.  Mark completed tasks as `[x]` in the group list.
    2.  Remove or uncheck the line in `**Current Active Groups**`.

### E. ADD NEW TASKS
-   **Goal**: Capture a new TODO found during work.
-   **Rule**:
    -   **Same Scope**: If it touches files in your *active* group, add it there.
    -   **Different Scope**: Create a **NEW** Task Group with a clear list of "Files Involved".

# Best Practices
-   **Granularity**: Task Groups should be "file-isolated" as much as possible. Group tasks by the *files* they touch, not just the feature.
-   **Safety**: If you see a lock, respect it. Do not edit files listed in a locked group's description.
-   **Responsibility**: You MUST unlock your group if you are done or if you are notifying the user to pause. Stale locks block everyone.
