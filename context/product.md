# Product Specification

## 1. Overview
-   **Product Name**: ContextManagement
-   **Vision**: To serve as the single source of truth and backup for global Antigravity configurations, including rules, skills, and workflows.
-   **Target Audience**: The Developer (User) and the AI Agent.
-   **Problem Solved**: Fragmentation of global configurations and lack of version control for AI behaviors.

## 2. Core Features (MVP)
-   **Rules Backup**: storage for `GEMINI.md`.
-   **Skills Repository**: storage for agent skills.
-   **Workflows Repository**: storage for agent workflows.

## 3. User Flow
-   User updates global config -> User/Agent syncs to this repo -> Changes are committed and pushed.
-   User sets up new environment -> User clones this repo -> Agent restores global configs.

## 4. Constraints & Non-Functional Requirements
-   Must maintain exact directory structure expected by the AI agent.
-   Files must be in Markdown or standard config formats.
