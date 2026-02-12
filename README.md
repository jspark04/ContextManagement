# ContextManagement

> Central repository for backing up and syncing global Antigravity configurations.

This repository serves as the source of truth for your AI agent's "brain," including global rules, learned skills, and standard workflows. By version-controlling these configurations, you ensure consistent agent behavior across different environments and sessions.

## 📂 Repository Structure

| Directory | Description |
| :--- | :--- |
| **`rules/`** | Contains global behavioral rules (e.g., `GEMINI.md`) that guide agent actions and communication style. |
| **`skills/`** | Repository of agent capabilities (e.g., `create_new_skill`, `update_context`) that extend functionality. |
| **`workflows/`** | Standard operating procedures and checklists (e.g., `retrospective.md`, `initialize_context.md`) for complex tasks. |
| **`context/`** | Project-specific context files (`product.md`, `architecture.md`, `tasks.md`) describing this repository itself. |

## 🚀 Getting Started

### Prerequisites

-   Windows OS (PowerShell 7+ recommended)
-   Antigravity Agent (configured with `.gemini` directory)

### Maintenance Workflow

Currently, synchronization is manual. Automation scripts are planned for future release.

#### Backup (Local -> Repo)
Copy your local global configurations to this repository to back them up:
```powershell
# Example: Copy GEMINI.md
Copy-Item "../.gemini/GEMINI.md" "./rules/GEMINI.md" -Force
```

#### Restore (Repo -> Local)
Apply repository configurations to your local environment:
```powershell
# Example: Restore Workflows
Copy-Item "./workflows/*" "../.gemini/antigravity/global_workflows/" -Recurse -Force
```

### Automation

A workspace workflow is available to sync global configurations to this repository:

1.  Run the workflow: `@[/sync_global_to_repo]` (requires agent support).
2.  Or manually execute the steps defined in `.agent/workflows/sync_global_to_repo.md`.

## 🤝 Contributing

1.  **Rules**: Updates to `GEMINI.md` affect all agent interactions. Test changes in a single session before committing.
2.  **Skills**: Ensure new skills follow the `SKILL.md` structure and are self-contained.
3.  **Workflows**: Document steps clearly in markdown. Use checklist format for agent-followable tasks.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
