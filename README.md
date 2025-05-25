Easy to use - run and go - just need edit with notepad and change path to webhook from discord - discord server -> settings -> share/or whatever is callled - new webhook -  copy/paste link
set "COPYPASTE WEBHOOK FROM DISCORD SERVER HERE" webhook from discord
set "SERVER_NAME=NAMEOFYOURSERVER/REALM HERE" for example WOW server
set "PROCESS_NAME=EXACTNAMEOFEXEFILEYOUWANTMONITOR" -> for example server.exe (same name as .exe file u want monitor)
FILE NEED TO BE IN SAME FOLDER AS .EXE TO MONITOR/U CAN USE R CLICK - SEND TO - DESKTOP if you want...its usable on all types of exe files.

Save as "StartMonitor.bat"
![image](https://github.com/user-attachments/assets/4623e9de-5e5d-4756-af67-8a948dc27f1f)


@echo off
setlocal EnableDelayedExpansion

:: === Nastavení proměnných ===
set "WEBHOOK=https://discord.com/api/"
set "SERVER_NAME=Realm or server name"
set "PROC_NAME=worldserver.exefilename"  :: jméno EXE bez .exe, včetně dvou mezer

:: Stavy – 0 = nikdy ještě neposláno, 1 = již odesláno
set "ONLINE=0"
set "OFFLINE=0"

:LOOP
  :: Hledáme jakýkoli proces, jehož řádek obsahuje PROC_NAME
  tasklist | findstr /I "%PROC_NAME%" >nul
  if errorlevel 1 (
    :: proces NEBĚŽÍ
    if "!OFFLINE!"=="0" (
      echo %DATE% %TIME% — %SERVER_NAME% OFFLINE
      powershell -NoProfile -Command ^
        "Invoke-RestMethod -Uri '%WEBHOOK%' -Method Post -Body (ConvertTo-Json @{content=':red_circle: **%SERVER_NAME% is OFFLINE!**'}) -ContentType 'application/json'"
      set "OFFLINE=1"
      set "ONLINE=0"
    )
  ) else (
    :: proces BĚŽÍ
    if "!ONLINE!"=="0" (
      echo %DATE% %TIME% — %SERVER_NAME% ONLINE
      powershell -NoProfile -Command ^
        "Invoke-RestMethod -Uri '%WEBHOOK%' -Method Post -Body (ConvertTo-Json @{content=':green_circle: **%SERVER_NAME% is ONLINE!**'}) -ContentType 'application/json'"
      set "ONLINE=1"
      set "OFFLINE=0"
    )
  )

  timeout /t 10 /nobreak >nul
goto LOOP

