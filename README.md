Easy to use - run and go - just need edit with notepad and change path to webhook from discord - discord server -> settings -> share/or whatever is callled - new webhook -  copy/paste link
set "COPYPASTE WEBHOOK FROM DISCORD SERVER HERE" webhook from discord
set "SERVER_NAME=NAMEOFYOURSERVER/REALM HERE" for example WOW server
set "PROCESS_NAME=EXACTNAMEOFEXEFILEYOUWANTMONITOR" -> for example server.exe (same name as .exe file u want monitor)
FILE NEED TO BE IN SAME FOLDER AS .EXE TO MONITOR/U CAN USE R CLICK - SEND TO - DESKTOP if you want...its usable on all types of exe files.

Save as "StartMonitor.bat"
![image](https://github.com/user-attachments/assets/4623e9de-5e5d-4756-af67-8a948dc27f1f)


@echo off
REM === Nastavení proměnných ===
set "COPYPASTE WEBHOOK FROM DISCORD SERVER HERE"
set "SERVER_NAME=NAMEOFYOURSERVER/REALM HERE"
set "PS1_NAME=monitor.ps1"
set "PROCESS_NAME=EXACTNAMEOFEXEFILEYOUWANTMONITOR"

REM === Vytvoření PowerShell skriptu ===
REM Odstraníme starý, pokud existuje:
if exist "%PS1_NAME%" del "%PS1_NAME%"

REM Zápis PowerShell kódu do monitor.ps1
echo # Monitoruje proces serveru a posílá status na Discord > "%PS1_NAME%"
echo $webhook = "%WEBHOOK%" >> "%PS1_NAME%"
echo $serverName = "%SERVER_NAME%" >> "%PS1_NAME%"
echo $onlineSent = $false >> "%PS1_NAME%"
echo $offlineSent = $false >> "%PS1_NAME%"
echo while ($true) { >> "%PS1_NAME%"
echo     $process = Get-Process -Name "%PROCESS_NAME%" -ErrorAction SilentlyContinue >> "%PS1_NAME%"
echo     if ($process) { >> "%PS1_NAME%"
echo         if (-not $onlineSent) { >> "%PS1_NAME%"
echo             Invoke-RestMethod -Uri $webhook -Method Post -Body (@{content=":green_circle: **$serverName is ONLINE!**"} ^| ConvertTo-Json) -ContentType "application/json" >> "%PS1_NAME%"
echo             $onlineSent = $true >> "%PS1_NAME%"
echo             $offlineSent = $false >> "%PS1_NAME%"
echo         } >> "%PS1_NAME%"
echo     } else { >> "%PS1_NAME%"
echo         if (-not $offlineSent) { >> "%PS1_NAME%"
echo             Invoke-RestMethod -Uri $webhook -Method Post -Body (@{content=":red_circle: **$serverName is OFFLINE!**"} ^| ConvertTo-Json) -ContentType "application/json" >> "%PS1_NAME%"
echo             $offlineSent = $true >> "%PS1_NAME%"
echo             $onlineSent = $false >> "%PS1_NAME%"
echo         } >> "%PS1_NAME%"
echo     } >> "%PS1_NAME%"
echo     Start-Sleep -Seconds 10 >> "%PS1_NAME%"
echo } >> "%PS1_NAME%"

REM === Spusť PowerShell skript v novém okně ===
start powershell -NoExit -ExecutionPolicy Bypass -File "%PS1_NAME%"

echo PowerShell monitor byl spuštěn v novém okně.
pause
