powershell-script1 TOP PROCESSES BY ACTIVE TCP CONNECTIONS
Get-NetTCPConnection | Group-Object OwningProcess | ForEach-Object { $proc = Get-Process -Id $_.Name -ErrorAction SilentlyContinue

[PSCustomObject]@{
    ProcessName = $proc.ProcessName
    PID         = $_.Name
    Connections = $_.Count
    CPU         = [math]::Round($proc.CPU,2)
    RAM_MB      = [math]::Round($proc.WorkingSet64/1MB,0)
    StartTime   = if($proc -and $proc.StartTime){
                     $proc.StartTime
                  }
                  else{
                     "N/A"
                  }
}
} | Sort-Object Connections -Descending | Format-Table ` @{Label='PROCESS NAME';Expression={$.ProcessName}}, @{Label='PID';Expression={$.PID}}, @{Label='TCP CONNECTIONS';Expression={$.Connections}}, @{Label='CPU SEC';Expression={$.CPU}}, @{Label='RAM (MB)';Expression={$.RAM_MB}}, @{Label='START TIME';Expression={$.StartTime}} -AutoSize
