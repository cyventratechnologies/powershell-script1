# PowerShell Script #1 - Top Processes by Active TCP Connections

## Script

```powershell
Get-NetTCPConnection |
Group-Object OwningProcess |
ForEach-Object {

    $proc = Get-Process -Id $_.Name -ErrorAction SilentlyContinue

    [PSCustomObject]@{
        ProcessName = $proc.ProcessName
        PID         = $_.Name
        Connections = $_.Count
        CPU         = [math]::Round($proc.CPU, 2)
        RAM_MB      = [math]::Round($proc.WorkingSet64 / 1MB, 0)
        StartTime   = if ($proc -and $proc.StartTime) {
            $proc.StartTime
        }
        else {
            "N/A"
        }
    }

} |
Sort-Object Connections -Descending |
Format-Table `
@{Label='PROCESS NAME';Expression={$_.ProcessName}},
@{Label='PID';Expression={$_.PID}},
@{Label='TCP CONNECTIONS';Expression={$_.Connections}},
@{Label='CPU SEC';Expression={$_.CPU}},
@{Label='RAM (MB)';Expression={$_.RAM_MB}},
@{Label='START TIME';Expression={$_.StartTime}} `
-AutoSize
```
