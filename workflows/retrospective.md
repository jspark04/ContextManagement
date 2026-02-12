---
description: Analyze the current session and update global GEMINI.md rules, and create new Skills/Workflows
---

# 🧠 Session Retrospective & Self-Improvement
This workflow analyzes the recent conversation history to improve the agent's long-term performance by updating global rules and creating new capabilities.

## Step 1: Analyze & Update Global Rules
Reflect on the session and update the global rules.
```prompt
Review our entire conversation history for this session. Identify:
1. **Recurring Corrections**: Did I have to be corrected on the same thing multiple times?
2. **Implied Preferences**: Did you express a preference (e.g., specific library, naming convention) that isn't yet in my global rules?
3. **Workflow Frictions**: Were there steps that felt clunky or required manual intervention where automation would have been better?
4. **Stack-Specific Patterns**: Are there best practices specific to the tech stack (e.g., Next.js, Go, Python) that I should remember for future projects using these tools?

**Action**:
Based on this analysis, carefully update the global rules file at `c:\Users\johnp\.gemini\GEMINI.md`.

**Guidelines for Updates**:
- **Consolidate**: Check if a rule already exists before adding it.
- **Categorize**: Place new rules under the existing headers:
    - `## ⚡ Operational Preferences`
    - `## 🛡️ Security Protocols`
    - `## 💻 Coding Standards`
    - `## ⚙️ Workflows & Automation`
- **Sub-categorize**: For stack-specific rules (Point #4), group them under `## 💻 Coding Standards` using sub-headers like `### React`, `### Python`, etc.
- **Consistency**: Match the existing tone and formatting (concise bullet points).

If meaningful updates are found, apply the changes to `c:\Users\johnp\.gemini\GEMINI.md`. If no updates are needed, simply report "No global rule updates required based on this session."
```

## Step 2: Expand Capabilities (Skills & Workflows)
Identify if new Skills or Workflows should be created to automate future tasks.
```prompt
Review the session for repeatable patterns or specialized knowledge that should be captured.

**Decision: Global vs. Workspace**
- **Global Scope**: Universal tools or patterns useful across *any* project.
    - *Action*: Use `create_new_skill` or `create_new_workflow` in global directories.
- **Workspace Scope**: Project-specific logic, repo-specific conventions, or domain rules.
    - *Action*: Create files in `<project_root>/.agent/skills` or `<project_root>/.agent/workflows`.

**Criteria for New Workflows**:
- **Repetitive Multi-Step Process**: 3+ commands that could be automated.
- **Complex Setup**: Disjointed steps to initialize a specific tool.

**Criteria for New Skills**:
- **Specialized Knowledge**: Tool/Library/API usage that required lookups.
- **Project-Specific Context**: Complex domain logic.

**Action**:
If a clear candidate exists, **CREATE** the file immediately.

**Reporting**:
- List any new files created and explain the scope decision (Global vs Workspace).
- If no new capabilities were needed, state "No new Skills or Workflows identified."
```