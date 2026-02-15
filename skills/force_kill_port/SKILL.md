---
description: robustly kill a process listening on a port (Windows/Linux) using escalation steps.
---

# Force Kill Port Process

This skill provides a definitive way to terminate processes holding a specific port, handling OS-specific nuances and "zombie" processes on Windows.

## Usage
When you need to clear a port (e.g., 8000) to start a server, follow this escalation path.

### Windows (PowerShell)

**Level 1: The Polite Kill (taskkill)**
Try this first. It identifies the PID and attempts a forced kill via standard Windows API.
```powershell
# Find and Kill in one go
Get-NetTCPConnection -LocalPort <PORT> -ErrorAction SilentlyContinue | ForEach-Object { 
    Write-Host "Killing PID $($_.OwningProcess)"
    taskkill /F /PID $_.OwningProcess 
}
```

**Level 2: The Nuclear Option (Stop-Process)**
If `taskkill` says "Access Denied" or "Process Not Found" but the port is still in use (check with `netstat -ano | findstr :<PORT>`), use PowerShell's native cmdlet.
```powershell
Stop-Process -Id <PID> -Force
```

**Level 3: The "Zombie" Scenario**
If `netstat` shows the port as `LISTENING` but `Get-Process` cannot find the PID, or `Stop-Process` fails with "No process found", you have a **Zombie Socket** (common with VS Code terminals or WSL interaction).
*   **Action**: Do NOT continue fighting.
*   **Resolution**: **Migrate your service** to `PORT + 1` (e.g., 8000 -> 8001). Updating one config file is faster than rebooting Windows.

### Linux / Mac
```bash
lsof -ti :<PORT> | xargs kill -9
```

## Anti-Patterns
*   **Looping Taskkill**: Do not run `taskkill` more than twice. If it fails twice, it's a zombie.
*   **Blind PID Guessing**: Always verify the PID exists before killing.
