# Misc

### Install Powershell Active Directory Module

```
Add-WindowsCapability –online –Name "Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0"
import-module activedirectory
```

### Increase kerberos ticket duration on linux machines

```
# edit /etc/sssd/sssd.conf

krb5_lifetime = 365d
krb5_renewable_lifetime = 365d
krb5_renew_interval = 1m
```

### Autologon Domain User

Download & run autologon64.exe by Sysinternals (this method is prefered). Alternatively you can also set the following registry entries on "HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon":

```
DefaultUserName     ...
DefaultPassword     ...
DefaultDomainName   ...
AutoAdminLogon      1
```

### Print NTLM Hash with Python

```python
import hashlib,binascii
hash = hashlib.new('md4', "".encode('utf-16le')).digest()
print(binascii.hexlify(hash))
```

### Find Default Password Policy Compliant Passwords in Wordlists

```
grep -n -P '(?=^.{8,255}$)(?=^[^\s]*$)(?=.*\d)(?=.*[A-Z])(?=.*[a-z])' rockyou.txt | less
```
