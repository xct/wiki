# Windows

### Named Pipes

Named Pipes are a remotely accessible, socket-like interface. Use [IONinja ](https://ioninja.com/downloads.html)to inspect traffic.

#### Resources 

* [The forgotten interface: Windows Named Pipes](https://hackinparis.com/data/slides/2017/2017_Cohen_Gil_The_forgotten_interface_Windows_named_pipes.pdf)

### FeedbackHub

```text
C:\Program Files\WindowsApps\Microsoft.WindowsFeedbackHub_1.1907.3152.0_x64__8wekyb3d8bbwe
```

Is a WinRT app, we have dlls and .winmd files. The .winmd files can be loaded into dnSpy, the dlls in Ida, which gives some type information.

### Path Redirection without Controlling File Contents

DoS: We can write to `C:\Windows\System32\en\Microsoft.Windows.Common-Controls.DLL` , which will prevent windows from booting.

### DACL

Consist of a series of ACE \(Access Control Entities\). Order matters \(first match principle\). Stored in SDDL format:

```text
ace_type;ace_flags;rights;object_guid;inherit_object_guid;account_sid
```

Example:

```text
(A;;RPWPCCDCLCSWRCWDWOGA;;;S-1-1-0)

AceType:
A = ACCESS_ALLOWED_ACE_TYPE
Access rights:
RP = ADS_RIGHT_DS_READ_PROP
WP = ADS_RIGHT_DS_WRITE_PROP
CC = ADS_RIGHT_DS_CREATE_CHILD
DC = ADS_RIGHT_DS_DELETE_CHILD
LC = ADS_RIGHT_ACTRL_DS_LIST
SW = ADS_RIGHT_DS_SELF
RC = READ_CONTROL
WD = WRITE_DAC
WO = WRITE_OWNER
GA = GENERIC_ALL

Ace Sid:
S-1-1-0
```

In an AD environment we can use PowerViews Get-ObjectAcl -Identity ... to view the ACLs \(in a more readable format, use ConvertFrom-SID to get names from SIDs\).

### Tools

* [https://github.com/hfiref0x/WinObjEx64](https://github.com/hfiref0x/WinObjEx64) 
* [http://www.zezula.net/en/fstools/filetest.html](http://www.zezula.net/en/fstools/filetest.html) 
* [https://github.com/googleprojectzero/sandbox-attacksurface-analysis-tools/tree/master/NtObjectManager](https://github.com/googleprojectzero/sandbox-attacksurface-analysis-tools/tree/master/NtObjectManager)

