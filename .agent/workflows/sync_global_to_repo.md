---
description: Sync global rules, skills, and workflows to this repository.
---

# Sync Global to Repo

This workflow copies the latest global configurations from your `.gemini` directory to this repository and reports a summary of all changes (Created/Updated).

## Steps

// turbo-all

### 1. Sync Rules, Skills, and Workflows
-   **Action**: Run smart sync script.
-   **Command**:
    ```powershell
    $globalPath = "$env:USERPROFILE\.gemini"
    $repoPath = Get-Location
    $changes = @()
    
    # Ensure directories exist
    if (-not (Test-Path "$repoPath\rules")) { New-Item -Path "$repoPath\rules" -ItemType Directory -Force | Out-Null }
    if (-not (Test-Path "$repoPath\skills")) { New-Item -Path "$repoPath\skills" -ItemType Directory -Force | Out-Null }
    if (-not (Test-Path "$repoPath\workflows")) { New-Item -Path "$repoPath\workflows" -ItemType Directory -Force | Out-Null }

    function Sync-Item {
        param($Source, $DestDir, $Type)
        if (-not (Test-Path $Source)) { return }

        $itemName = Split-Path $Source -Leaf
        $destPath = Join-Path $DestDir $itemName
        $action = "None"
        $isFile = (Get-Item $Source).PSIsContainer -eq $false

        if (-not (Test-Path $destPath)) {
            $action = "Created"
        } else {
            # Check for changes
            $shouldUpdate = $false
            if ($isFile) {
                # File hash check
                if ((Get-FileHash $Source).Hash -ne (Get-FileHash $destPath).Hash) { $shouldUpdate = $true }
            } else {
                # Directory check (SKILL.md)
                $srcFile = Join-Path $Source "SKILL.md"
                $destFile = Join-Path $destPath "SKILL.md"
                if ((Test-Path $srcFile) -and (Test-Path $destFile)) {
                    if ((Get-FileHash $srcFile).Hash -ne (Get-FileHash $destFile).Hash) { $shouldUpdate = $true }
                } elseif ((Test-Path $srcFile) -or (Test-Path $destFile)) {
                     # One has SKILL.md, one doesn't -> likely updated
                     $shouldUpdate = $true
                }
            }
            if ($shouldUpdate) { $action = "Updated" }
        }

        if ($action -ne "None") {
            Copy-Item -Path $Source -Destination $DestDir -Recurse -Force
            $global:changes += [PSCustomObject]@{
                Type = $Type
                Name = $itemName
                Action = $action
            }
        }
    }

    # 1. Rules
    Sync-Item -Source "$globalPath\GEMINI.md" -DestDir "$repoPath\rules" -Type "Rule"

    # 2. Skills
    $skillsSrc = "$globalPath\antigravity\skills"
    if (Test-Path $skillsSrc) {
        Get-ChildItem $skillsSrc -Directory | ForEach-Object {
            Sync-Item -Source $_.FullName -DestDir "$repoPath\skills" -Type "Skill"
        }
    }

    # 3. Workflows
    $wkfSrc = "$globalPath\antigravity\global_workflows"
    if (Test-Path $wkfSrc) {
        Get-ChildItem $wkfSrc -Filter "*.md" | ForEach-Object {
            Sync-Item -Source $_.FullName -DestDir "$repoPath\workflows" -Type "Workflow"
        }
    }

    # Report
    if ($global:changes.Count -gt 0) {
        Write-Host "`nSync Summary:" -ForegroundColor Cyan
        $global:changes | Sort-Object Type, Name | Format-Table -AutoSize
    } else {
        Write-Host "`nNo changes detected. Everything is up to date." -ForegroundColor Green
    }
    ```

### 2. Verify Status
-   **Action**: Check git status for any changes not caught or extra verification.
-   **Command**:
    ```powershell
    git status
    ```
