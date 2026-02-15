---
name: write_commit_message
description: Best practices and standards for writing clean, semantic git commit messages using the Conventional Commits specification. Use this whenever you are about to commit changes.
---

# Instructions

## 1. The Format
**Structure**:
```text
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## 2. Allowed Types
-   **feat**: A new feature (correlates with MINOR in Semantic Versioning).
-   **fix**: A bug fix (correlates with PATCH in Semantic Versioning).
-   **docs**: Documentation only changes.
-   **style**: Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc).
-   **refactor**: A code change that neither fixes a bug nor adds a feature.
-   **perf**: A code change that improves performance.
-   **test**: Adding missing tests or correcting existing tests.
-   **build**: Changes that affect the build system or external dependencies (example scopes: gulp, broccoli, npm).
-   **ci**: Changes to our CI configuration files and scripts (example scopes: Travis, Circle, BrowserStack, SauceLabs).
-   **chore**: Other changes that don't modify src or test files.
-   **revert**: Reverts a previous commit.

## 3. Best Practices Rules
-   **Imperative Mood**: Write the description as if giving a command (e.g., "add feature", not "added feature").
-   **No Period**: Do not end the subject line with a period.
-   **Length**: Keep the subject line under 50 characters if possible, max 72.
-   **Body**: Use the body to explain *what* and *why* vs. *how*. separate body from subject with a blank line.
-   **Breaking Changes**: Denote breaking changes with a `!` after the type/scope (e.g., `feat!: drop support for Node 6`) or in the footer (`BREAKING CHANGE: ...`).

## 4. Examples
-   `feat(auth): add login with google`
-   `fix: crash when user has no avatar`
-   `docs: update readme with setup instructions`
-   `style: format code with prettier`
-   `refactor!: drop support for v1 api`

# Constraints
-   **Adherence**: STRICTLY follow the `<type>: <description>` format.
-   **Context**: If a User Story or Issue ID is available, include it in the Footer (e.g., `Refs: #123`).
