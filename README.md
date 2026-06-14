# powershell-script1

Clear-Host

Write-Host "============================================================" -ForegroundColor Cyan
Write-Host " TOP PROCESSES BY ACTIVE TCP CONNECTIONS" -ForegroundColor Yellow
Write-Host "============================================================" -ForegroundColor Cyan

Write-Host ""
Write-Host "What does this show?" -ForegroundColor Green
Write-Host "Displays which Windows processes currently own the most TCP network connections." -ForegroundColor White

Write-Host ""
Write-Host "Why is it useful?" -ForegroundColor Green
Write-Host "• Identify applications generating the most network traffic" -ForegroundColor White
Write-Host "• Spot unexpected or suspicious network activity" -ForegroundColor White
Write-Host "• Troubleshoot connectivity issues" -ForegroundColor White
Write-Host "• Quickly correlate process, memory, CPU and network usage" -ForegroundColor White

Write-Host ""
Write-Host "Fields Explained:" -ForegroundColor Green
Write-Host "Connections = Number of TCP connections owned by the process" -ForegroundColor White
Write-Host "CPU         = Total CPU seconds consumed since process start" -ForegroundColor White
Write-Host "RAM_MB      = Current memory consumption (Working Set)" -ForegroundColor White
Write-Host "StartTime   = Process launch time" -ForegroundColor White

Write-Host ""
Write-Host "==================== LIVE RESULTS ====================" -ForegroundColor Magenta

Get-NetTCPConnection |
Group-Object OwningProcess |
ForEach-Object {

    $proc = Get-Process -Id $_.Name -ErrorAction SilentlyContinue

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
} |
Sort-Object Connections -Descending |
Format-Table `
@{Label='PROCESS NAME';Expression={$_.ProcessName}},
@{Label='PID';Expression={$_.PID}},
@{Label='TCP CONNECTIONS';Expression={$_.Connections}},
@{Label='CPU SEC';Expression={$_.CPU}},
@{Label='RAM (MB)';Expression={$_.RAM_MB}},
@{Label='START TIME';Expression={$_.StartTime}} -AutoSize
