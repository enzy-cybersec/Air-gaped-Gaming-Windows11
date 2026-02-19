# Air-gaped-Gaming-Windows11
This is project on top of atlasos to airgap and remove unnecessary services to improve performance.

# Discription
This project is for offline gaming and not daily use or any other use since it is air-gaped and all of the security tools are **removed**.  
I have used windows regestries mostly to make the changed.  
In this setup I also Use VR and Immersed so it also includes the parts related to that. Feel free to skip them.

# Note
Please make sure you have installed all the needed drivers like GPU and so on before doing this!

# Steps

**1. Disables all window, taskbar, and menu animations for zero-latency feel**  
```
reg add "HKCU\Control Panel\Desktop" /v "UserPreferencesMask" /t REG_BINARY /d 9012038010000000 /f
reg add "HKCU\Control Panel\Desktop\WindowMetrics" /v "MinAnimate" /t REG_SZ /d 0 /f
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ListviewAlphaSelect" /t REG_DWORD /d 0 /f
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\VisualEffects" /v "VisualFXSetting" /t REG_DWORD /d 2 /f
```  
**2. Remove Edge**  
```
$EdgePath = (Get-ChildItem "C:\Program Files (x86)\Microsoft\Edge\Application" -Include "setup.exe" -Recurse); if ($EdgePath) {Start-Process -FilePath $EdgePath.FullName -ArgumentList "--uninstall --system-level --verbose-logging --force-uninstall" -Wait}
```

**3. Disables Wi-Fi/Bluetooth hardware**  
```
Get-NetAdapter | Where-Object { $_.PhysicalMediaType -like "*Native 802.11*" -or $_.MediaType -like "*71*" } | Disable-NetAdapter -Confirm:$false
```  
**4. Remove Defender**  
1. Open Windows Security.  
2. Go to Virus & threat protection settings > Manage settings.  
3. Toggle Tamper Protection to Off. 
4. In powershell with admin rights:
```
# 1. Remove the Windows Defender App package from the system
Get-AppxPackage *Microsoft.Windows.SecurityHealthCare* | Remove-AppxPackage
Get-AppxPackage *Windows.Defender* -AllUsers | Remove-AppxPackage

# 2. Permanently disable the boot-start drivers for Defender
sc.exe config WinDefend start= disabled
sc.exe config WdNisSvc start= disabled
sc.exe config Sense start= disabled
sc.exe config WdBoot start= disabled
sc.exe config WdFilter start= disabled

# 3. Wipe the Defender folders (Take Ownership first)
# Note: This is aggressive and ensures no "self-healing" occurs.
takeown /f "C:\ProgramData\Microsoft\Windows Defender" /r /d y
icacls "C:\ProgramData\Microsoft\Windows Defender" /grant administrators:F /t
Remove-Item -Path "C:\ProgramData\Microsoft\Windows Defender" -Recurse -Force
```  
**5. SYSTEM: Stop Auto-Updates forever**  
```
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoUpdate" /t REG_DWORD /d 1 /f
```  
**6. DISABLE TELEMETRY**  
```
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v "AllowTelemetry" /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\AppCompat" /v "AITEnable" /t REG_DWORD /d 0 /f
sc.exe config DiagTrack start= disabled
sc.exe config dmwappushservice start= disabled
```  
**7. PRIORITIZE GAMES (VR & Immersed Focus)**  
```
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile" /v "SystemResponsiveness" /t REG_DWORD /d 10 /f
```
**8 .Configures the Games profile for "High" Priority and "GPU Priority"**  
```
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "GPU Priority" /t REG_DWORD /d 8 /f
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Priority" /t REG_DWORD /d 6 /f
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games" /v "Scheduling Category" /t REG_SZ /d "High" /f
```
**9. The "Ultimate Performance" Power Plan**  
1. Unlocks the hidden 'Ultimate Performance' power plan  
```
powercfg -duplicatescheme e9a42b02-d5df-448d-aa00-03f14749eb61
```  
2. Sets the plan as active  
```
powercfg -setactive e9a42b02-d5df-448d-aa00-03f14749eb61
```  
3. Disables global Power Throttling  
```
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Power\PowerThrottling" /v "PowerThrottlingOff" /t REG_DWORD /d 1 /f
```  
4. Remove Search icon from taskbar  
```
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Search" /v "SearchboxTaskbarMode" /t REG_DWORD /d 0 /f
```  
5. Remove Task View and Widgets  
```
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowTaskViewButton" /t REG_DWORD /d 0 /f
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarDa" /t REG_DWORD /d 0 /f
```  
**10. Clean Taskbar UI**  
```
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Search" /v "SearchboxTaskbarMode" /t REG_DWORD /d 0 /f
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "ShowTaskViewButton" /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Dsh" /v "AllowNewsAndInterests" /t REG_DWORD /d 0 /
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "TaskbarMn" /t REG_DWORD /d 0 /f
```
