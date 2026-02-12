---
name: update_gemini_md
description: Safely updates the global `GEMINI.md` rules file by reading, analyzing, and consolidating rules. Use this when the user asks to "update my rules", "add a pref", or "save this learning".
---

# Instructions
1.  **Read Current Rules**: ALWAY start by reading the current `C:\Users\johnp\.gemini\GEMINI.md` file using `view_file`.
    -   *Why?* To avoid overwriting existing rules or creating duplicates.
2.  **Analyze Request**: Identify what specific rule or preference the user wants to add/update.
    -   *Conflict Check*: Does this contradict an existing rule? If so, ask the user for clarification (unless the user explicitly said "change/overwrite").
    -   *Redundancy Check*: Is this already covered?
3.  **Formulate Update**:
    -   **Consolidate**: If a similar section exists (e.g., "Coding Standards"), add the new rule there.
    -   **Format**: Use the existing Markdown structure (Headers, bullet points).
    -   **Clarity**: Write the rule clearly and concisely.
4.  **Apply Update**: Use `replace_file_content` (for specific insertions) or `write_to_file` (if re-writing a whole section/file, but be careful).
    -   *Tip*: Using `replace_file_content` to append to a list is safer than rewriting the whole file.
5.  **Verify**: Read the file again to ensure the Markdown is valid and the change looks correct.
6.  **Notify**: Confirm the rule has been added/updated.

# Constraints
-   **File Path**: ALWAYS use `C:\Users\johnp\.gemini\GEMINI.md`.
-   **Safety**: NEVER overwrite the entire file without reading it first.
-   **Style**: maintain the `Tone & Style` defined in the file itself!
